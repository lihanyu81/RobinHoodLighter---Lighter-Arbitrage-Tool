<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-1f6feb?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-1f6feb?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### Follow me on **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### Rebate services **BN20 OK40 BG40 GATE60 BYBIT40, long-term cooperation available**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### AI top-up **GPTPRO 130 5X 620 20X 1230 with after-sales support**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# RobinHood Lighter ↔ Lighter Arbitrage Tool

This tool monitors order-book spreads between RobinHood Lighter (RBLighter) and Lighter and executes two-sided spread trades according to your configuration.

RBLighter and Lighter use similar trading principles but are independent exchange instances. Their order books, accounts, API keys, market indexes, and account states are separate. The tool reads both markets and sends both legs in parallel only when strategy and risk conditions are satisfied.

The public release is a single-file Linux x86_64 executable. It does not contain source code, user configuration, databases, or trading credentials.

---

## Important risk notice

This tool involves digital-asset trading. A visible spread does not guarantee profit. Results can be affected by:

- Trading fees, funding fees, and other platform charges
- Network latency, market movement, and order-book depth
- Slippage, partial fills, failed cancellations, or mismatched fills
- API, WebSocket, signing-service, or exchange failures
- Margin, position, liquidation, and account-permission risks
- Changes to RobinHood Lighter or Lighter rules, APIs, or availability
- Laws, regulations, and regional access restrictions

Start with dry-run mode and small amounts. Confirm the accounts, API keys, market indexes, order directions, and risk settings before enabling live trading.

This project is not investment advice and does not guarantee returns. You assume all trading, account, and fund risks.

## Core logic

- Reads real-time order books from RBLighter and Lighter.
- Calculates executable spreads in both directions and maintains sliding statistics.
- Allows entry only after the minimum sample count is reached and the current spread exceeds the configured threshold.
- Sends both legs as IOC orders in parallel, encoding prices with each exchange's precision and maximum slippage.
- Advances a task only after both fills, quantities, and final positions reconcile.
- Sends single-leg, partial-fill, delayed-position, and unknown-state cases through compensation, pause, or recovery flows.
- Periodically reads positions, mark prices, and liquidation prices; risk triggers use reduce-only orders to restore a safe baseline.
- Restores saved tasks after stop or restart. Unknown states fail closed and do not blindly resend an economic intent.

## RBLighter referral-account check

Before live startup, the program verifies that the configured RBLighter account belongs to the configured referral relationship:

1. Query the referrer's public Account Index.
2. Use referral-owner credentials to read the invited-wallet list.
3. Resolve every invited wallet to its Account Index and build the dynamic scope.
4. Check whether `RBLIGHTER_ACCOUNT_INDEX` belongs to the referrer or an invited wallet.
5. Reject immediately if it does not; API-key validation, task restoration, and account streams are not started.
6. Only after the relationship check passes, validate the trading API key and start live services.

New invitees are discovered on the next startup. The registration link is:

<https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

If the trading account is not the referrer account, configure dedicated referral-owner credentials:

```env
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

When these fields are empty, the program falls back to `RBLIGHTER_API_KEY_INDEX` and `RBLIGHTER_API_PRIVATE_KEY`. This fallback works only when that key belongs to the referral owner. Keep both API private keys confidential.

## Official interfaces

- RBLighter REST: <https://api.rh.lighter.xyz>
- RBLighter WebSocket: <wss://api.rh.lighter.xyz/stream>
- Lighter REST: <https://mainnet.zklighter.elliot.ai>
- Lighter WebSocket: <wss://mainnet.zklighter.elliot.ai/stream>
- RobinHood Lighter API docs: <https://apidocs.rh.lighter.xyz/docs/get-started>

Configure separate accounts and API keys for both exchanges. Never put Lighter credentials in the RBLighter fields or vice versa.

## Linux environment

The current single-file release targets Linux x86_64 and recommends glibc 2.31 or newer, at least 2 CPU cores, 2 GB RAM, and a stable low-latency network.

Recommended providers include Vultr, Alibaba Cloud, and Tencent Cloud. Pricing, credits, promotions, and availability change frequently; check the provider's official page.

Recommended server specifications:

| Item | Recommendation |
|---|---|
| Operating system | Ubuntu 22.04/24.04 or another mainstream Linux distribution |
| CPU | 2 cores or more |
| Memory | 2 GB or more |
| Architecture | x86_64 |
| Disk | 20 GB or more |
| Network | Stable and low latency |

Check the architecture:

```bash
uname -m
```

The result must be `x86_64`. An `aarch64` or `arm64` server needs a separately built ARM release.

## Download the Linux executable

```bash
mkdir -p ~/rblighter-arbitrage
cd ~/rblighter-arbitrage

curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
./panda-arb-0.1.0-linux-x64-onefile --help
```

The checksum must report `panda-arb-0.1.0-linux-x64-onefile: OK`. Do not run a failed or mismatched download.

## External configuration and data

The `.env` file and database are external to the executable:

```bash
mkdir -p ~/.config/panda-arb ~/panda-arb-data
./panda-arb-0.1.0-linux-x64-onefile config init \
  --output ~/.config/panda-arb/.env
chmod 600 ~/.config/panda-arb/.env
```

Edit `~/.config/panda-arb/.env`:

```env
DRY_RUN=true
LIVE_TRADING_ACK=false
POC_VERIFIED=false
ENABLE_REAL_MARKET_STREAMS=false

LIGHTER_BASE_URL=https://mainnet.zklighter.elliot.ai
LIGHTER_WS_URL=wss://mainnet.zklighter.elliot.ai/stream
LIGHTER_ACCOUNT_INDEX=
LIGHTER_API_KEY_INDEX=
LIGHTER_API_PRIVATE_KEY=

RBLIGHTER_BASE_URL=https://api.rh.lighter.xyz
RBLIGHTER_WS_URL=wss://api.rh.lighter.xyz/stream
RBLIGHTER_ACCOUNT_INDEX=
RBLIGHTER_API_KEY_INDEX=
RBLIGHTER_API_PRIVATE_KEY=

RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Create and configure separate trading API keys for both exchanges. Never send API private keys, wallet keys, seed phrases, or tokens to anyone or commit them to GitHub.

Check the configuration and runtime dependencies:

```bash
./panda-arb-0.1.0-linux-x64-onefile config check \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data

./panda-arb-0.1.0-linux-x64-onefile doctor \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --network
```

## Enabling live trading

1. Keep `DRY_RUN=true` and verify startup.
2. Confirm both exchange URLs, accounts, API keys, and market indexes.
3. Confirm market data, mark prices, positions, and account status.
4. Test IOC, partial fills, reduce-only orders, and fill reports with minimal funds.
5. Test a complete two-leg open/close cycle and restart recovery.
6. Review risk limits and emergency-exit behavior.
7. Only then set:

```env
DRY_RUN=false
LIVE_TRADING_ACK=true
POC_VERIFIED=true
ENABLE_REAL_MARKET_STREAMS=true
```

Live startup performs the RBLighter referral check first. If it fails, the program does not validate the trading API key, restore tasks, or start account streams.

## Start the service

Recommended local-only binding:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8000 \
  --no-browser
```

Create an SSH tunnel from your computer:

```bash
ssh -L 8000:127.0.0.1:8000 USER@SERVER_IP
```

Open `http://127.0.0.1:8000` locally. Prefer the SSH tunnel instead of exposing the management UI publicly.

For an explicitly controlled public binding:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 0.0.0.0 \
  --port 8000 \
  --no-browser
```

Restrict the cloud firewall to your own IP. Do not open `0.0.0.0/0` unless you fully understand the security consequences.

## Background operation

For a temporary background process:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8000 \
  --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

For a public background process (only when firewall and access control are configured):

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 0.0.0.0 \
  --port 8000 \
  --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

View logs and stop the recorded process:

```bash
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

Use `kill -9` only when the process cannot exit normally and the PID has been verified. For long-running deployments, prefer `systemd` with least-privilege permissions.

For automatic startup after reboot and restart after failure, use a `systemd` service instead.

## Troubleshooting

Check port 8000:

```bash
sudo ss -ltnp | grep ':8000'
sudo lsof -nP -iTCP:8000 -sTCP:LISTEN
```

- `Permission denied`: run `chmod +x panda-arb-0.1.0-linux-x64-onefile`.
- `Exec format error`: verify `uname -m` is `x86_64`.
- Account rejected: verify the RBLighter Account Index belongs to the referrer or an invitee, and configure referral-owner credentials when trading from an invitee account.
- API-key failure: ensure the API-key index, API private key, and Account Index belong to the same exchange account.
- Port occupied: identify the PID with `lsof`, stop it normally, or choose another port.

If the Account Index relationship check fails, the application exits before API-key validation, task restoration, or account streams. The configuration file is selected by `--env`; the database and runtime state are selected by `--data-dir`. Keep both outside the executable so upgrades preserve state.

If the port is occupied, verify the PID before stopping it or choose another port:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8001 \
  --no-browser
```

## Package contents

The Linux executable includes the Python runtime, project dependencies, browser assets, and the Linux x86_64 signer. It does not include `.env` files, API private keys, wallet keys, referral-owner keys, databases, logs, runtime data, or source code.

The repository publishes only the executable, its SHA-256 file, documentation, and translated READMEs. Always verify a new executable before upgrading and keep the external configuration and data directory.

## Support

Telegram community: <https://t.me/+e4p8Vq1ABGthODM1>

When reporting an issue, provide the Linux version, CPU architecture, tool version, command, sanitized logs, and non-sensitive `doctor` output. Never send private keys, seed phrases, full `.env` files, API tokens, databases, or other credentials.
