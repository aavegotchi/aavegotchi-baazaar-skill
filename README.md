# aavegotchi-baazaar-skill

This repo contains the `aavegotchi-baazaar` skill folder (docs-only) for interacting with the Aavegotchi Baazaar on Base mainnet:
- View listings via the Goldsky subgraph
- Add listings (ERC721 + ERC1155)
- Execute purchases with GHST (direct) or USDC (`swapAndBuy*`)
- Safety-first `dryRun` default (simulate with `~/.foundry/bin/cast call`, only broadcast with `~/.foundry/bin/cast send` when explicitly set)

Skill contents:
- `aavegotchi-baazaar/SKILL.md`
- `aavegotchi-baazaar/references/`

Install targets (kept in sync separately):
- Local (Clawd): `/Users/coderdan/Documents/GitHub/clawd-workspace/skills/aavegotchi-baazaar/`
- VPS (OpenClaw): `/home/agent/.openclaw/skills/aavegotchi-baazaar/`

## Optional Secure Alternative: Coinbase CDP Server Wallet

This repo still documents and supports the current private-key broadcast flow.

For teams that want policy-controlled server signing and no raw private key in `.env`, see:
- `aavegotchi-baazaar/references/cdp-integration.md`

This is docs-only integration guidance, not a hard runtime migration.
