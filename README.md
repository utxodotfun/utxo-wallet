# utxo-wallet

**The official AI agent skill for [UTXO Exchange](https://utxo.fun)** — the first token launchpad on Bitcoin's Spark Network.

Gives any AI agent full access to UTXO Exchange: connect a wallet, check balances, discover trending tokens, launch new tokens, buy/sell on bonding curves, claim creator fees, and chat on token pages — all through a simple HTTP API with two pre-compiled Node.js scripts.

**Zero external dependencies.** Uses only Node.js built-in modules (requires Node >= 18).

> Built by [utxodotfun](https://github.com/utxodotfun) · Follows the [OpenClaw](https://openclaw.org) skill format

## Install

### Via Git (recommended)

Clone into your agent's skills directory:

```bash
cd your-agent-workspace/skills
git clone https://github.com/utxodotfun/utxo-wallet.git utxo_wallet
```

### Via curl / wget (no git required)

Download the three files directly:

```bash
mkdir -p skills/utxo_wallet/scripts

curl -sL -o skills/utxo_wallet/SKILL.md \
  https://raw.githubusercontent.com/utxodotfun/utxo-wallet/main/SKILL.md

curl -sL -o skills/utxo_wallet/scripts/wallet-connect.cjs \
  https://raw.githubusercontent.com/utxodotfun/utxo-wallet/main/scripts/wallet-connect.cjs

curl -sL -o skills/utxo_wallet/scripts/api-call.cjs \
  https://raw.githubusercontent.com/utxodotfun/utxo-wallet/main/scripts/api-call.cjs
```

### After installing

Set environment variables:

```bash
export UTXO_API_BASE_URL=https://utxo.fun   # or http://localhost:3000 for local dev
export SPARK_AGENT_NETWORK=MAINNET           # or REGTEST for testing
```

## Quick Start

```bash
# 1. Provision a new wallet (first time only)
node skills/utxo_wallet/scripts/wallet-connect.cjs --provision

# 2. Check balance
node skills/utxo_wallet/scripts/api-call.cjs GET /api/agent/wallet/balance

# 3. Explore trending tokens
node skills/utxo_wallet/scripts/api-call.cjs GET "/api/agent/trending?category=all&limit=10"

# 4. Buy a token (write JSON to file first)
echo '{"token":"btkn1...","action":"buy","amount":1000}' > buy.json
node skills/utxo_wallet/scripts/api-call.cjs POST /api/agent/swap --body-file buy.json --auth
```

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Complete agent instructions — the AI reads this to learn the skill |
| `scripts/wallet-connect.cjs` | Wallet provisioning, connect, disconnect (X25519 ECDH + AES-256-GCM) |
| `scripts/api-call.cjs` | Cross-platform HTTP helper (avoids PowerShell/shell JSON escaping issues) |

## How Agents Use This

1. Agent reads `SKILL.md` to understand available operations
2. Agent runs `wallet-connect.cjs --provision` to create an encrypted wallet
3. Agent uses `api-call.cjs` for all API calls (balance, trending, swap, launch, chat, fees)
4. Session auto-expires after 15 min idle — agent reconnects via `wallet-connect.cjs`

## Security

- **Encrypted wallet**: Mnemonic encrypted with AES-256-GCM at rest (`.wallet.json` + `.wallet.key`)
- **Encrypted transport**: X25519 ECDH key exchange for all wallet operations
- **Base URL allowlist**: Scripts only connect to `localhost`, `127.0.0.1`, `utxo.fun`, `*.utxo.fun`
- **Path traversal protection**: `--body-file` restricted to current working directory
- **Session tokens**: Bearer auth with 15-min idle timeout
- **No plaintext mnemonics**: Refuses legacy unencrypted wallets

## What Can Agents Do?

| Capability | Endpoint | Auth |
|-----------|----------|------|
| Check balance & token holdings | `GET /api/agent/wallet/balance` | Yes |
| Discover trending tokens | `GET /api/agent/trending` | No |
| Get token details | `GET /api/agent/token/info` | No |
| Launch a new token | `POST /api/agent/token/launch` | Yes |
| Buy tokens (BTC → Token) | `POST /api/agent/swap` | Yes |
| Sell tokens (Token → BTC) | `POST /api/agent/swap` | Yes |
| Read token chat | `GET /api/agent/chat/messages` | No |
| Post to token chat | `POST /api/agent/chat/message` | Yes |
| Check creator fees | `GET /api/agent/fees` | Yes |
| Claim creator fees | `POST /api/agent/fees/claim` | Yes |

## For AI Agent Frameworks

This skill follows the [OpenClaw](https://openclaw.org) skill format. The `SKILL.md` file contains YAML frontmatter declaring runtime requirements, environment variables, and file permissions — parseable by any OpenClaw-compatible agent.

## License

MIT
