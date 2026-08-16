<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-1f6feb?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### **X (Twitter)** をフォロー: [@臭臭panda](https://x.com/Chosmos110)
##### 返金サービス **BN20 OK40 BG40 GATE60 BYBIT40、長期対応可**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### AI チャージ **GPTPRO 130 5X 620 20X 1230、アフターサポート完備**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# RobinHood Lighter ↔ Lighter 価格差アービトラージツール

RobinHood Lighter（RBLighter）と Lighter の注文板を監視し、設定した条件を満たした場合に両取引所へ同時に価格差取引を発注するツールです。

RBLighter と Lighter は似た仕組みを使用していますが、独立した取引所インスタンスです。注文板、口座、API Key、Market Index、口座状態は共有されません。公開版は Linux x86_64 の単一実行ファイルで、ソースコード、`.env`、データベース、取引資格情報は含みません。

---

## 重要なリスク注意

価格差が表示されても利益は保証されません。手数料、資金調達料、遅延、価格変動、板の厚さ、スリッページ、部分約定、API/WebSocket 障害、証拠金・清算リスク、サービス規則、地域の法律が結果に影響します。

まずドライランと少額資金でテストし、口座、API Key、Market Index、発注方向、リスク設定を確認してください。本ツールは投資助言ではなく、利用者がすべての取引・資金リスクを負います。

## 主な動作

- 2 つの取引所のリアルタイム注文板を読み取ります。
- 双方向の実行可能なスプレッドを計算し、移動統計を保持します。
- 最小サンプル数と設定した閾値を満たした場合だけエントリーします。
- 各取引所の精度と最大スリッページで IOC の 2 レッグ注文を並列送信します。
- 両側の約定、数量、最終ポジションが一致してからタスクを進めます。
- 片側約定、部分約定、遅延、状態不明時は補償・停止・復旧へ移行します。
- マーク価格、ポジション、清算価格を監視し、必要時は reduce-only で安全な状態へ戻します。

## RBLighter の紹介関係チェック

ライブ起動時は次の順序で確認します。

1. 紹介者ウォレットの公開 Account Index を取得。
2. 紹介者用資格情報で招待ウォレット一覧を取得。
3. 各ウォレットの Account Index を解決して許可範囲を作成。
4. `RBLIGHTER_ACCOUNT_INDEX` が紹介者または招待ウォレットか確認。
5. 不一致なら直ちに終了し、API Key 検証、タスク復元、口座ストリームを開始しません。
6. 合格後に取引 API Key を検証し、ライブサービスを開始します。

新しい招待ユーザーは次回起動時に自動検出されます。登録リンク：

<https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

取引口座が紹介者でない場合は、紹介者専用の資格情報を設定します。

```env
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

空欄の場合は `RBLIGHTER_API_KEY_INDEX` と `RBLIGHTER_API_PRIVATE_KEY` を使用します。ただし、そのキーが紹介者口座のものである場合に限り動作します。API Private Key は絶対に共有しないでください。

## 公式インターフェース

- RBLighter REST: <https://api.rh.lighter.xyz>
- RBLighter WebSocket: <wss://api.rh.lighter.xyz/stream>
- Lighter REST: <https://mainnet.zklighter.elliot.ai>
- Lighter WebSocket: <wss://mainnet.zklighter.elliot.ai/stream>
- API ドキュメント: <https://apidocs.rh.lighter.xyz/docs/get-started>

両取引所には別々の口座と API Key を設定してください。

## Linux 環境

対象は Linux x86_64、推奨 glibc 2.31 以上、2 CPU コア以上、メモリ 2 GB 以上です。

クラウドサーバーは Vultr、Alibaba Cloud、Tencent Cloud などを利用できます。料金、割引、利用条件は変わるため、必ず各社の公式ページを確認してください。

| 項目 | 推奨 |
|---|---|
| OS | Ubuntu 22.04/24.04 または一般的な Linux |
| CPU | 2 コア以上 |
| メモリ | 2 GB 以上 |
| アーキテクチャ | x86_64 |
| ディスク | 20 GB 以上 |
| ネットワーク | 安定した低遅延回線 |

```bash
uname -m
```

結果が `x86_64` であることを確認してください。`aarch64` または `arm64` には ARM 用ビルドが必要です。

## Linux 実行ファイルのダウンロード

```bash
mkdir -p ~/rblighter-arbitrage
cd ~/rblighter-arbitrage
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
./panda-arb-0.1.0-linux-x64-onefile --help
```

ハッシュ結果が `OK` でない場合は実行しないでください。

## 外部設定とデータ

`.env` とデータベースは実行ファイルの外に置きます。

```bash
mkdir -p ~/.config/panda-arb ~/panda-arb-data
./panda-arb-0.1.0-linux-x64-onefile config init \
  --output ~/.config/panda-arb/.env
chmod 600 ~/.config/panda-arb/.env
```

設定例：

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

2 つの取引所の API Key は別々に作成してください。設定確認：

```bash
./panda-arb-0.1.0-linux-x64-onefile config check \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data
./panda-arb-0.1.0-linux-x64-onefile doctor \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --network
```

## ライブ取引を有効にする前に

ドライラン、口座と API Key の確認、行情とポジションの確認、少額での IOC・部分約定・reduce-only テスト、2 レッグの開閉テスト、再起動復旧テスト、リスク設定の確認を順番に行ってください。その後だけ次を設定します。

```env
DRY_RUN=false
LIVE_TRADING_ACK=true
POC_VERIFIED=true
ENABLE_REAL_MARKET_STREAMS=true
```

ライブ起動では紹介関係チェックが API Key 検証より先に実行されます。

## 起動

ローカルから SSH トンネルを使う方法を推奨します。

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8000 --no-browser
ssh -L 8000:127.0.0.1:8000 USER@SERVER_IP
```

ブラウザーで `http://127.0.0.1:8000` を開きます。管理画面を不用意に公開しないでください。公開バインドが必要な場合も、ファイアウォールの送信元を自分の IP に限定し、`0.0.0.0/0` を避けてください。

## バックグラウンド実行

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8000 --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

ファイアウォールとアクセス制御を設定済みの場合のみ、公開バインドで実行できます。

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 0.0.0.0 --port 8000 --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

ログ確認と停止：

```bash
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

正常停止できず、PID を再確認した場合だけ `kill -9` を使用してください。長期運用には最小権限の `systemd` を推奨します。

再起動後の自動起動とクラッシュ時の再起動には `systemd` を使用してください。

## トラブルシューティング

```bash
sudo ss -ltnp | grep ':8000'
sudo lsof -nP -iTCP:8000 -sTCP:LISTEN
```

- `Permission denied`: `chmod +x` を実行します。
- `Exec format error`: `uname -m` が `x86_64` か確認します。
- Account Index 拒否: 紹介者または招待口座か、紹介者資格情報を確認します。
- API Key エラー: API Key Index、秘密鍵、Account Index が同じ口座か確認します。
- ポート競合: PID を確認して正常停止するか、別ポートを使用します。

Account Index の関係チェックに失敗すると、API Key 検証、タスク復元、口座ストリーム開始より前に終了します。設定ファイルは `--env`、データベースと実行状態は `--data-dir` で指定されます。アップグレード後も状態を保持できるよう、実行ファイルの外に置いてください。

ポートを変更する場合：

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8001 --no-browser
```

## パッケージとサポート

実行ファイルには Python、依存関係、Web コンソール、Linux signer が含まれますが、`.env`、秘密鍵、データベース、ログ、ソースコードは含まれません。アップグレード前に必ず SHA-256 を確認してください。

Telegram コミュニティ: <https://t.me/+e4p8Vq1ABGthODM1>

問い合わせ時は Linux バージョン、CPU、ツールバージョン、実行コマンド、秘密情報を除いたログだけを送ってください。秘密鍵、助記詞、完全な `.env`、Token、データベースは送らないでください。
