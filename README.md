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
