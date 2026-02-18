# Optional Integration: Coinbase CDP Server Wallet (No `PRIVATE_KEY` in `.env`)

This guide is an optional security upgrade for signing and broadcasting transactions.

It does **not** replace the existing skill flow today:
- Existing `cast call` / `cast send --private-key` instructions in this repo remain supported.
- Use this guide when you want policy-controlled server signing and no raw private key in environment variables.

## Why Use CDP Here

Threat model improvements:
- No raw EOA private key in `.env`.
- Transaction policy controls (chain, contract, method, spend constraints).
- Deterministic prepare -> execute flow to reduce phishing/prompt-injection risk.

References:
- CDP Server Wallet v2 Quickstart: https://docs.cdp.coinbase.com/server-wallets/v2/quickstart
- CDP Policy Engine API: https://docs.cdp.coinbase.com/api-reference/v2/rest-api/policy-engine/create-a-policy
- OpenClaw tool approvals: https://docs.openclaw.ai/tools/exec-approvals

## Prerequisites

- Coinbase CDP project with Server Wallet access.
- Base mainnet target (`chainId=8453`).
- Existing Baazaar config from `SKILL.md` (`DIAMOND`, `GHST`, `USDC`, `SUBGRAPH_URL`).
- `cast` installed for calldata/selector checks.

## Secure Credentials Pattern (Encrypted File Path)

Use a credentials file path, not plaintext env vars:

```bash
export CDP_CREDENTIALS_PATH="$HOME/.openclaw/secrets/cdp-baazaar.json.gpg"
export CDP_DECRYPT_CMD='gpg --quiet --decrypt'
```

Example decrypted JSON:

```json
{
  "apiKeyId": "cdp_api_key_id",
  "apiKeySecret": "cdp_api_key_secret",
  "walletSecret": "cdp_wallet_secret",
  "networkId": "base-mainnet",
  "accountAddress": "0xYourSignerAddress",
  "policyId": "optional_policy_id"
}
```

Recommended file mode before encryption: `chmod 600`.

## Wallet Bootstrap: Import vs Create

### Path A: Import existing EOA (recommended when preserving approvals/state)

Use this when you want to preserve the current `FROM_ADDRESS` balances and token approvals.

1. Export encrypted source key (one-time migration material).
2. Import into CDP Server Wallet account using CDP tooling/API.
3. Verify imported address matches current `FROM_ADDRESS`.

### Path B: Create a new CDP wallet

Use this when rotating infrastructure and starting from a new signer.

1. Create wallet/account in CDP.
2. Fund it and re-approve `GHST`/`USDC`/NFT approvals to `DIAMOND`.
3. Update automation to use the new `accountAddress`.

## Policy Setup Guidance (Baazaar)

Restrict `evm.sendTransaction` to:
- Chain: Base mainnet only.
- Contract allowlist: `DIAMOND`, `GHST`, `USDC`, approved NFT contracts.
- Method allowlist: only Baazaar-required methods.

Selector derivation examples:

```bash
cast sig "approve(address,uint256)"
cast sig "setApprovalForAll(address,bool)"
cast sig "executeERC721ListingToRecipient(uint256,address,uint256,uint256,address)"
cast sig "executeERC1155ListingToRecipient(uint256,address,uint256,uint256,uint256,address)"
cast sig "swapAndBuyERC721(address,uint256,uint256,uint256,uint256,address,uint256,uint256,address)"
cast sig "swapAndBuyERC1155(address,uint256,uint256,uint256,uint256,address,uint256,uint256,uint256,address)"
cast sig "addERC721Listing(address,uint256,uint256,uint256)"
cast sig "setERC1155Listing(address,uint256,uint256,uint256,uint256)"
```

If your policy system supports spend caps, set:
- Per-tx max value.
- Daily aggregate max value.

## Prepare -> Execute Intent Pattern (Anti-Phishing)

Use a two-step flow:

1. `prepare`: build tx from validated onchain/subgraph state.
2. `execute`: require explicit operator confirmation on the frozen intent.

Recommended guardrails:
- Intent TTL (example: 300s).
- Intent hash must include chainId, `to`, calldata, value, and state snapshot IDs.
- Reject execution if listing state drifted since prepare.

Example intent record:

```json
{
  "intentId": "sha256:...",
  "action": "execute_erc721_listing",
  "chainId": 8453,
  "to": "0xA99c4B08201F2913Db8D28e71d020c4298F29dBF",
  "data": "0x...",
  "value": "0x0",
  "createdAt": "2026-02-18T00:00:00.000Z",
  "expiresAt": "2026-02-18T00:05:00.000Z",
  "stateProof": {
    "listingId": "123",
    "priceInWei": "...",
    "subgraphBlock": "..."
  },
  "status": "prepared"
}
```

## OpenClaw Hardening Checklist

- Keep wallet/broadcast tool disabled by default; allowlist only when needed.
- Keep command execution approvals enabled for any broadcast path.
- Never run dynamic user strings (`eval`, `bash -c`, `sh -c`).
- Validate all addresses and uint inputs before encoding calldata.

References:
- https://docs.openclaw.ai/tools
- https://docs.openclaw.ai/sandboxing
- https://docs.openclaw.ai/tools/exec-approvals

## Troubleshooting

- Policy deny: verify chain, destination, method selector, and value caps.
- Address mismatch: confirm imported/created CDP account address.
- Replay/idempotency issues: bind idempotency key to `intentId`.
- State drift: re-run `prepare` after listing changes.

## Fallback (Current Legacy Flow)

If CDP integration is unavailable, continue using existing documented flow in `SKILL.md`:
- Simulate with `cast call`.
- Broadcast with `cast send --private-key` only when explicitly instructed.

This repo has **not** hard-migrated runtime behavior in this change.
