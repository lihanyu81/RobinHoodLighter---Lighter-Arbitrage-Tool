<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-1f6feb?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### 关注我 **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### 返佣业务 **各大平台返佣BN20 OK40 BG40 GATE60 BYBIT40 长期可加**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### AI代充 **完美售后 GPTPRO 130 5X 620 20X 1230**: [@熊猫寨自营业务](https://service.pandazhai.com/products)
---

# RobinHood Lighter ↔ Lighter 价差套利工具

[![Version](https://img.shields.io/badge/version-0.2.0-blue)](#)
[![Platform](https://img.shields.io/badge/platform-Linux%20x86__64-orange)](#)
[![GitHub Stars](https://img.shields.io/github/stars/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool?style=social)](https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/stargazers)

本工具用于监控 RobinHood Lighter（RBLighter）与 Lighter 之间的订单簿价差，并根据配置执行双边价差交易。

RBLighter 与 Lighter 使用相近的交易原理，但属于两个独立的交易所实例，订单簿、账户、API Key、Market Index 和账户状态彼此独立。本项目通过同时读取两边行情，在满足策略和风控条件时并行执行两腿订单。

当前公开发布版本为 Linux x86_64 单文件程序，不包含项目源码、用户配置、数据库或交易凭据。

---

## 使用前须知

本工具涉及数字资产交易。价差出现不代表一定能够获利，实际结果可能受到以下因素影响：

- 交易手续费、资金费率和其他平台费用
- 网络延迟、行情变化和订单簿深度
- 滑点、部分成交、撤单失败或两边成交不同步
- API、WebSocket、签名服务或交易所异常
- 保证金、仓位、强平和账户权限风险
- RobinHood Lighter 或 Lighter 的规则、接口和服务状态变化
- 用户所在国家或地区的法律、监管和平台访问限制

请先使用模拟模式和小额资金完成测试，确认账户、API Key、Market Index、下单方向和风控参数均正确后，再考虑启用实盘。

本工具不构成投资建议，不承诺收益。使用者应自行承担交易、账户和资金风险。

---

## 核心逻辑

- 读取 RBLighter 与 Lighter 的实时订单簿。
- 分别计算两个方向的可执行价差，并维护滑动统计窗口。
- 达到最小样本数后，只有当前价差超过策略阈值才允许入场。
- 两腿使用 IOC 订单并行发送，价格按照各自交易所的精度和最大滑点编码。
- 只有两边成交回报、成交数量和最终仓位都完成对账，任务才会推进到下一步。
- 单腿成交、部分成交、仓位延迟或状态不明确时，进入补偿、暂停或恢复流程。
- 定时读取两边仓位、标记价和强平价，触发风控时使用 reduce-only 订单恢复到安全状态。
- 停止或重启后会恢复已保存任务；无法确认状态时默认暂停开仓，不猜测性重复下单。

---

## RBLighter 账户关系校验

实盘启动前，程序会先核验 RBLighter 账户是否属于指定邀请关系：

1. 将 `RBLIGHTER_ACCOUNT_INDEX` 提交到只读推荐关系验证服务。
2. 验证服务在自己的服务器上刷新推荐名单并返回 `approved`。
3. 检查 `approved` 是否为 `true`；不通过时立即终止，不会继续验证交易 API Key，也不会恢复任务或启动账户流。
4. 通过账户关系校验后，才验证交易账户 API Key 并启动实盘服务。

验证服务会在请求时刷新推荐名单。拒绝提示会提供注册链接：

<https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

验证服务地址可通过以下配置覆盖：

```env
RBLIGHTER_REFERRAL_VERIFICATION_URL=http://43.165.190.162:8001
```

推荐人 API 私钥只保存在验证服务上，不需要配置到交易程序所在服务器。

---

## 官方接口

- RBLighter REST：<https://api.rh.lighter.xyz>
- RBLighter WebSocket：<wss://api.rh.lighter.xyz/stream>
- Lighter REST：<https://mainnet.zklighter.elliot.ai>
- Lighter WebSocket：<wss://mainnet.zklighter.elliot.ai/stream>
- RobinHood Lighter API 文档：<https://apidocs.rh.lighter.xyz/docs/get-started>

两边的账户和 API Key 必须分别配置，不能把 Lighter 的账户或 API Key 填入 RBLighter 配置。

---

## Linux 运行环境

当前发布的单文件程序适用于：

- Linux x86_64
- 建议使用 glibc 2.31 或更高版本
- 建议 2 核 CPU、2 GB 或以上内存
- 稳定、低延迟的网络环境

可根据所在地区、支付方式和网络质量选择云服务器：

1. [Vultr](https://www.vultr.com/?ref=9915549-9J)
2. [阿里云](https://www.aliyun.com/minisite/goods?userCode=bwzu4y9m)
3. [腾讯云](https://curl.qcloud.com/eOn6o376)

| 项目 | 建议配置 |
|---|---|
| 操作系统 | Ubuntu 22.04/24.04 或其他主流 Linux |
| CPU | 2 核及以上 |
| 内存 | 2 GB 及以上 |
| 架构 | x86_64 |
| 磁盘 | 20 GB 及以上 |
| 网络 | 稳定、低延迟 |
| 地区 | 推荐：日本 |

优惠活动、赠金条件和有效期可能随时变化，请以厂商官方页面显示的信息为准。

检查服务器架构：

```bash
uname -m
```

输出应为：

```text
x86_64
```

如果输出为 `aarch64` 或 `arm64`，当前 Linux x64 文件不能直接运行，需要单独构建 ARM 版本。

---

## 下载 Linux 单文件版本

在服务器上执行：

```bash
mkdir -p ~/rblighter-arbitrage
cd ~/rblighter-arbitrage

curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.2.0-linux-x64-onefile
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.2.0-linux-x64-onefile.sha256
```

校验文件：

```bash
sha256sum -c panda-arb-0.2.0-linux-x64-onefile.sha256
```

必须看到：

```text
panda-arb-0.2.0-linux-x64-onefile: OK
```

校验失败、文件不存在或哈希值不一致时，不要继续运行，应重新下载并确认仓库地址。

添加执行权限并查看帮助：

```bash
chmod +x panda-arb-0.2.0-linux-x64-onefile
./panda-arb-0.2.0-linux-x64-onefile --help
```

---

## 配置文件与数据目录

`.env` 和数据库不会封装在可执行文件中。建议将配置和运行数据放在程序目录之外：

```bash
mkdir -p ~/.config/panda-arb ~/panda-arb-data
./panda-arb-0.2.0-linux-x64-onefile config init \
  --output ~/.config/panda-arb/.env
chmod 600 ~/.config/panda-arb/.env
```

编辑配置文件：

```bash
nano ~/.config/panda-arb/.env
```

基础配置示例：

```env
# 安全开关：首次使用保持以下配置
DRY_RUN=true
LIVE_TRADING_ACK=false
POC_VERIFIED=false
ENABLE_REAL_MARKET_STREAMS=false

# Lighter
LIGHTER_BASE_URL=https://mainnet.zklighter.elliot.ai
LIGHTER_WS_URL=wss://mainnet.zklighter.elliot.ai/stream
LIGHTER_ACCOUNT_INDEX=
LIGHTER_API_KEY_INDEX=
LIGHTER_API_PRIVATE_KEY=

# RBLighter
RBLIGHTER_BASE_URL=https://api.rh.lighter.xyz
RBLIGHTER_WS_URL=wss://api.rh.lighter.xyz/stream
RBLIGHTER_ACCOUNT_INDEX=
RBLIGHTER_API_KEY_INDEX=
RBLIGHTER_API_PRIVATE_KEY=

# Optional notifications. Secrets are read from environment only and never persisted/logged.
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=

```

必须分别创建并填写两边交易账户的 API Key。API 私钥、钱包私钥、助记词和 Token 不要发送给任何人，也不要提交到 GitHub。

检查配置：

```bash
./panda-arb-0.2.0-linux-x64-onefile config check \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data
```

检查依赖、签名库和网络：

```bash
./panda-arb-0.2.0-linux-x64-onefile doctor \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --network
```

---

## 实盘启用流程

建议严格按照以下顺序操作：

1. 使用 `DRY_RUN=true` 验证程序可以启动。
2. 确认两边 API 地址、账户、API Key 和 Market Index 属于正确交易所。
3. 确认两边行情、标记价、仓位和账户状态正常。
4. 使用最小资金验证 IOC、部分成交、reduce-only 和成交回报。
5. 完成双腿开仓、平仓和重启恢复测试。
6. 确认风险参数和紧急退出流程符合预期。
7. 最后才设置：

```env
DRY_RUN=false
LIVE_TRADING_ACK=true
POC_VERIFIED=true
ENABLE_REAL_MARKET_STREAMS=true
```

实盘启动时，程序会先执行 RBLighter 邀请关系校验。校验失败时不会验证交易 API Key，也不会执行 `restore_tasks` 或启动账户流。

---

## 启动服务

推荐只监听本机地址：

```bash
./panda-arb-0.2.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8000 \
  --no-browser
```

然后在本地电脑建立 SSH 隧道：

```bash
ssh -L 8000:127.0.0.1:8000 用户名@服务器IP
```

在本地浏览器打开：

```text
http://127.0.0.1:8000
```

除非已经配置好访问控制、HTTPS 和防火墙规则，否则不要将管理页面直接暴露到公网。

如果确实需要监听公网地址：

```bash
./panda-arb-0.2.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 0.0.0.0 \
  --port 8000 \
  --no-browser
```

公网访问会增加管理页面、账户状态和交易控制暴露风险。安全组来源地址应尽量限制为自己的固定 IP，不要直接开放 `0.0.0.0/0`。

---

## 后台运行

SSH 隧道后台运行：

```bash
nohup ./panda-arb-0.2.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8000 \
  --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &

echo $! > ~/panda-arb-data/panda-arb.pid
```

公网后台运行：

```bash
nohup ./panda-arb-0.2.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 0.0.0.0 \
  --port 8000 \
  --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &

echo $! > ~/panda-arb-data/panda-arb.pid
```

查看日志：

```bash
tail -f ~/panda-arb-data/panda-arb.log
```

停止程序：

```bash
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

只有在程序无法正常退出并确认 PID 无误时，才使用：

```bash
kill -9 "$(cat ~/panda-arb-data/panda-arb.pid)"
```

长期运行建议使用 `systemd`，并为配置文件和数据目录设置最小权限。

---

## 端口检查与故障排查

检查 8000 端口：

```bash
sudo ss -ltnp | grep ':8000'
```

或：

```bash
sudo lsof -nP -iTCP:8000 -sTCP:LISTEN
```

常见问题：

### `Permission denied`

重新添加执行权限：

```bash
chmod +x panda-arb-0.2.0-linux-x64-onefile
```

### `Exec format error`

检查服务器架构：

```bash
uname -m
```

当前文件只支持 `x86_64`，不能直接在 ARM 服务器运行。

### RBLighter Account Index 被拒绝

确认：

- `RBLIGHTER_ACCOUNT_INDEX` 填写的是正确账户的 Account Index。
- 该账户属于邀请人或已邀请钱包。
- 推荐关系验证服务地址可访问并返回有效的 `approved` 字段。
- RBLighter API 地址没有误填为 Lighter 地址。

账户关系校验失败时，程序会在 API Key 验证前退出，不会启动交易流。

### API Key 验证失败

确认交易账户、API Key Index、API 私钥和 Account Index 是同一账户的完整组合。Lighter 与 RBLighter 的 API Key 不能混用。

### 8000 端口被占用

先查询占用进程：

```bash
sudo lsof -nP -iTCP:8000 -sTCP:LISTEN
```

确认 PID 后正常停止：

```bash
kill PID
```

也可以使用其他端口：

```bash
./panda-arb-0.2.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 \
  --port 8001 \
  --no-browser
```

### 配置文件或数据库在哪里

配置文件由 `--env` 指定，数据库和运行数据由 `--data-dir` 指定。推荐将两者放在可执行文件之外，便于升级程序时保留任务状态和配置。

---

## 封包与文件说明

Linux 单文件程序已经包含：

- Python 运行时
- 项目依赖
- 浏览器控制台静态资源
- Lighter Linux x86_64 原生签名库

程序包不包含：

- 用户 `.env`
- Lighter 或 RBLighter API 私钥
- 邀请人查询私钥
- 数据库和历史任务数据
- 日志和其他运行时文件
- 项目源码

当前仓库只发布可执行文件和 SHA-256 校验文件。升级程序时，请先校验新文件，再使用原来的外置配置和数据目录启动。

---

## 支持与反馈

Telegram 社区：<https://t.me/+HmFWrmcW9w01YzFl>

反馈问题时可以提供：

- Linux 发行版和版本
- CPU 架构
- 程序版本
- 执行的命令
- 已脱敏的错误日志
- `doctor` 的非敏感检查结果

请勿提供：

- API 私钥或钱包私钥
- 助记词
- `.env` 完整内容
- API Token
- 数据库文件
- 能够控制账户或资金的其他凭据
