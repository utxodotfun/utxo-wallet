# utxo-wallet

AI agent skill for [UTXO Exchange](https://utxo.fun) on Bitcoin's Spark Network.

Enables any AI agent to: connect a wallet, check balances, discover tokens, launch tokens, buy/sell tokens, and chat on token pages — all through a simple HTTP API with two pre-compiled scripts.

**Zero external dependencies.** Uses only Node.js built-in modules (requires Node >= 18).

## Install

### Via ClawHub (recommended)

```bash
npx clawhub install utxo-wallet
```

### Via GitHub (manual)

Clone into your agent's skills directory:

```bash
cd your-agent-workspace/skills
git clone https://github.com/DavidYashar/utxo-wallet.git utxo_wallet
```

Then set environment variables:

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
3. Agent uses `api-call.cjs` for all API calls (balance, trending, swap, launch, chat)
4. Session auto-expires after 15 min idle — agent reconnects via `wallet-connect.cjs`

## Security

- **Encrypted wallet**: Mnemonic encrypted with AES-256-GCM at rest (`.wallet.json` + `.wallet.key`)
- **Encrypted transport**: X25519 ECDH key exchange for all wallet operations
- **Base URL allowlist**: Scripts only connect to `localhost`, `127.0.0.1`, `utxo.fun`, `*.utxo.fun`
- **Path traversal protection**: `--body-file` restricted to current working directory
- **Session tokens**: Bearer auth with 15-min idle timeout
- **No plaintext mnemonics**: Refuses legacy unencrypted wallets

## For AI Agent Frameworks

This skill follows the [OpenClaw](https://openclaw.org) skill format. The `SKILL.md` file contains YAML frontmatter declaring runtime requirements, environment variables, and file permissions — parseable by any OpenClaw-compatible agent.

## License

MIT
