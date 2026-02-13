# Aavegotchi OpenClaw Skills

This repo contains OpenClaw skills for the Aavegotchi ecosystem on Base mainnet.

## Skills

### aavegotchi-baazaar

Docs-only skill for interacting with the Aavegotchi Baazaar:
- View listings via the Goldsky subgraph
- Add listings (ERC721 + ERC1155)
- Execute purchases with GHST (direct) or USDC (`swapAndBuy*`)
- Safety-first `dryRun` default (simulate with `cast call`, only broadcast with `cast send` when explicitly set)

**Contents:**
- `aavegotchi-baazaar/SKILL.md`
- `aavegotchi-baazaar/references/`

### pet-me-master 💜

Interactive Aavegotchi petting via Bankr. Daily kinship ritual - less automation, more connection.

**Philosophy:** Daily ritual over automation. Build emotional bonds through interaction.

**Features:**
- Interactive petting via Bankr wallet (no private key exposure)
- On-chain cooldown checking via Foundry cast
- Batch mode: pet all ready gotchis in ONE transaction
- Multi-gotchi support with status dashboard
- Countdown timers for next pet time
- Smart skipping of waiting gotchis

**Commands:**
- `pet my gotchi` - Check & pet (first gotchi)
- `pet all my gotchis` - Batch pet all ready ones ⭐
- `pet status` - Show all gotchis + countdowns
- `pet gotchi #ID` - Pet specific gotchi

**Contents:**
- `pet-me-master/SKILL.md`
- `pet-me-master/README.md`
- `pet-me-master/scripts/`
- `pet-me-master/references/`

**GitHub:** https://github.com/aaigotchi/pet-me-master

---

## Installation

Install via ClawHub (when published):

```bash
clawhub install aavegotchi-baazaar
clawhub install pet-me-master
```

Or clone this repo and symlink the skill folders to your OpenClaw workspace.

---

Made with 💜 by the Aavegotchi community

LFGOTCHi! 👻🦞🚀
