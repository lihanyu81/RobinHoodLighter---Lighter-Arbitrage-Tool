<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-1f6feb?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### **X (Twitter)** 팔로우: [@臭臭panda](https://x.com/Chosmos110)
##### 리베이트 서비스 **BN20 OK40 BG40 GATE60 BYBIT40, 장기 이용 가능**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### AI 충전 **GPTPRO 130 5X 620 20X 1230, 사후 지원 제공**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# RobinHood Lighter ↔ Lighter 차익거래 도구

이 도구는 RobinHood Lighter(RBLighter)와 Lighter의 주문장을 모니터링하고, 설정된 조건이 충족되면 두 거래소에 양쪽 주문을 실행합니다.

RBLighter와 Lighter는 비슷한 거래 원리를 사용하지만 서로 독립된 거래소 인스턴스입니다. 주문장, 계정, API Key, Market Index, 계정 상태를 공유하지 않습니다. 공개 배포판은 Linux x86_64 단일 실행 파일이며 소스 코드, `.env`, 데이터베이스, 거래 자격 증명을 포함하지 않습니다.

---

## 중요한 위험 안내

가격 차이가 보인다고 해서 수익이 보장되는 것은 아닙니다. 거래 수수료, 펀딩비, 지연, 가격 변동, 호가 깊이, 슬리피지, 부분 체결, API/WebSocket 오류, 증거금·청산 위험, 거래소 규정과 지역 법규가 결과에 영향을 줍니다.

먼저 드라이런과 소액으로 테스트하십시오. 계정, API Key, Market Index, 주문 방향과 위험 설정을 확인한 뒤 실거래를 활성화하십시오. 이 도구는 투자 조언이 아니며 모든 거래 및 자금 위험은 사용자가 부담합니다.

## 핵심 동작

- RBLighter와 Lighter의 실시간 주문장을 읽습니다.
- 양방향 실행 가능 스프레드를 계산하고 이동 통계를 유지합니다.
- 최소 샘플 수와 임계값을 충족할 때만 진입합니다.
- 각 거래소의 정밀도와 최대 슬리피지를 적용한 IOC 양쪽 주문을 병렬로 전송합니다.
- 양쪽 체결, 수량, 최종 포지션이 대사된 후에만 작업을 진행합니다.
- 한쪽 체결, 부분 체결, 지연 또는 알 수 없는 상태는 보상·일시정지·복구 절차로 처리합니다.
- 포지션, 마크 가격, 청산 가격을 주기적으로 확인하고 필요하면 reduce-only 주문으로 안전 상태를 복구합니다.

## RBLighter 추천 계정 확인

실거래 시작 전 다음 순서를 지킵니다.

1. 추천인 지갑의 공개 Account Index를 조회합니다.
2. 추천인 자격 증명으로 초대 지갑 목록을 조회합니다.
3. 각 지갑의 Account Index를 확인해 동적 허용 범위를 만듭니다.
4. `RBLIGHTER_ACCOUNT_INDEX`가 추천인 또는 초대 계정인지 검사합니다.
5. 범위 밖이면 즉시 종료하며 API Key 검증, 작업 복구, 계정 스트림을 시작하지 않습니다.
6. 통과한 뒤 거래 API Key를 검증하고 실거래 서비스를 시작합니다.

새 초대 사용자는 다음 시작 시 자동으로 조회됩니다. 가입 링크:

<https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

거래 계정이 추천인 계정이 아니라면 다음 추천인 전용 자격 증명을 설정하십시오.

```env
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

두 값이 비어 있으면 `RBLIGHTER_API_KEY_INDEX`와 `RBLIGHTER_API_PRIVATE_KEY`를 사용합니다. 단, 해당 키가 추천인 계정의 키인 경우에만 동작합니다. API Private Key는 절대 공유하지 마십시오.

## 공식 인터페이스

- RBLighter REST: <https://api.rh.lighter.xyz>
- RBLighter WebSocket: <wss://api.rh.lighter.xyz/stream>
- Lighter REST: <https://mainnet.zklighter.elliot.ai>
- Lighter WebSocket: <wss://mainnet.zklighter.elliot.ai/stream>
- API 문서: <https://apidocs.rh.lighter.xyz/docs/get-started>

두 거래소의 계정과 API Key는 반드시 별도로 설정하십시오.

## Linux 환경

현재 배포판은 Linux x86_64용입니다. glibc 2.31 이상, CPU 2코어 이상, 메모리 2GB 이상과 안정적인 저지연 네트워크를 권장합니다.

Vultr, Alibaba Cloud, Tencent Cloud 등의 서버를 사용할 수 있습니다. 요금, 할인, 크레딧과 제공 조건은 변경될 수 있으므로 각 업체의 공식 페이지를 확인하십시오.

| 항목 | 권장 |
|---|---|
| 운영체제 | Ubuntu 22.04/24.04 또는 주요 Linux 배포판 |
| CPU | 2코어 이상 |
| 메모리 | 2GB 이상 |
| 아키텍처 | x86_64 |
| 디스크 | 20GB 이상 |
| 네트워크 | 안정적인 저지연 연결 |

```bash
uname -m
```

결과가 `x86_64`인지 확인하십시오. `aarch64` 또는 `arm64`에는 별도의 ARM 빌드가 필요합니다.

## Linux 실행 파일 다운로드

```bash
mkdir -p ~/rblighter-arbitrage
cd ~/rblighter-arbitrage
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
curl -fLO https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
./panda-arb-0.1.0-linux-x64-onefile --help
```

해시가 `OK`가 아니면 실행하지 마십시오.

## 외부 설정과 데이터

`.env`와 데이터베이스는 실행 파일 외부에 저장합니다.

```bash
mkdir -p ~/.config/panda-arb ~/panda-arb-data
./panda-arb-0.1.0-linux-x64-onefile config init \
  --output ~/.config/panda-arb/.env
chmod 600 ~/.config/panda-arb/.env
```

설정 예시:

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

두 거래소의 API Key는 별도로 생성하십시오. 설정 확인:

```bash
./panda-arb-0.1.0-linux-x64-onefile config check \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data
./panda-arb-0.1.0-linux-x64-onefile doctor \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --network
```

## 실거래 활성화 절차

드라이런 시작, 계정/API Key 확인, 시세·포지션 확인, 소액 IOC·부분 체결·reduce-only 테스트, 양쪽 개장/청산과 재시작 복구 테스트, 위험 설정 검토를 순서대로 진행하십시오. 그 후에만 다음을 설정합니다.

```env
DRY_RUN=false
LIVE_TRADING_ACK=true
POC_VERIFIED=true
ENABLE_REAL_MARKET_STREAMS=true
```

실거래 시작 시 추천 관계 확인이 API Key 검증보다 먼저 실행됩니다.

## 서비스 시작

SSH 터널을 사용하는 로컬 바인딩을 권장합니다.

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8000 --no-browser
ssh -L 8000:127.0.0.1:8000 USER@SERVER_IP
```

브라우저에서 `http://127.0.0.1:8000`을 엽니다. 관리 화면을 공개 인터넷에 노출하지 마십시오. 공개 바인딩이 필요하면 방화벽의 소스 IP를 제한하고 `0.0.0.0/0`을 피하십시오.

## 백그라운드 실행

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8000 --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

방화벽과 접근 제어를 설정한 경우에만 공개 바인딩을 사용하십시오.

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 0.0.0.0 --port 8000 --no-browser \
  > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

로그 확인 및 정상 종료:

```bash
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

정상 종료가 되지 않고 PID를 다시 확인한 경우에만 `kill -9`를 사용하십시오. 장기 운영에는 최소 권한의 `systemd`를 권장합니다.

재부팅 후 자동 시작과 장애 후 재시작에는 `systemd`를 사용하십시오.

## 문제 해결

```bash
sudo ss -ltnp | grep ':8000'
sudo lsof -nP -iTCP:8000 -sTCP:LISTEN
```

- `Permission denied`: `chmod +x`를 실행합니다.
- `Exec format error`: `uname -m`이 `x86_64`인지 확인합니다.
- Account Index 거부: 추천인 또는 초대 계정인지, 추천인 자격 증명이 설정되었는지 확인합니다.
- API Key 오류: API Key Index, Private Key, Account Index가 같은 계정인지 확인합니다.
- 포트 충돌: PID를 확인해 정상 종료하거나 다른 포트를 사용합니다.

Account Index 관계 확인이 실패하면 API Key 검증, 작업 복구, 계정 스트림 시작 전에 프로그램이 종료됩니다. 설정 파일은 `--env`, 데이터베이스와 실행 상태는 `--data-dir`로 지정합니다. 업그레이드 시 상태를 유지하려면 실행 파일 외부에 보관하십시오.

다른 포트를 사용하려면:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8001 --no-browser
```

## 패키지와 지원

실행 파일에는 Python, 의존성, 웹 콘솔, Linux signer가 포함되지만 `.env`, 키, 데이터베이스, 로그, 소스 코드는 포함되지 않습니다. 업그레이드 전 SHA-256을 확인하십시오.

Telegram 커뮤니티: <https://t.me/+e4p8Vq1ABGthODM1>

문의 시 Linux 버전, CPU 아키텍처, 도구 버전, 실행 명령과 민감정보를 제거한 로그만 보내십시오. Private Key, 시드 문구, 전체 `.env`, Token, 데이터베이스는 보내지 마십시오.
