<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-1f6feb?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### Folge mir auf **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### Cashback-Service **BN20 OK40 BG40 GATE60 BYBIT40, langfristig verfügbar**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### AI-Aufladung **GPTPRO 130 5X 620 20X 1230 mit Kundendienst**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# RobinHood Lighter ↔ Lighter Spread-Arbitrage-Tool

Folge mir auf **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)

## Überblick

Dieses Tool vergleicht die Orderbücher von RobinHood Lighter (RBLighter) und Lighter. Überschreitet der nach Gebühren und Slippage berechnete Spread die konfigurierten Grenzen, werden Orders auf beiden Märkten platziert und ihre Ausführung überwacht.

> ⚠️ **Risiko**: Arbitrage ist nicht garantiert profitabel. Preisbewegungen, Slippage, Latenz, Netzwerkfehler und Liquidation können Verluste verursachen. Zuerst immer mit `DRY_RUN=true` und kleinen Beträgen testen.

## Kernablauf

1. Beste Bids/Asks beider Orderbücher abrufen.
2. Spread nach Gebühren und Slippage berechnen.
3. Mindestgröße, Genauigkeit, Guthaben und Risikolimits prüfen.
4. Eine Order auf jedem Markt senden.
5. Fills und Positionen überwachen; bei Bedarf stornieren, wiederholen oder reduzieren.

## RBLighter-Referral-Prüfung

Beim Start wird zuerst der öffentliche Account Index des Einladenden abgefragt. Danach werden mit den Referral-Zugangsdaten alle eingeladenen Wallets und deren Account Indexes ermittelt. `RBLIGHTER_ACCOUNT_INDEX` muss zu einem erlaubten Index gehören. Bei einem nicht erlaubten Wert beendet sich das Programm sofort; erst nach erfolgreicher Prüfung werden API-Key, Tasks und Account-Streams gestartet.

Für eine eingeladene Trading-Konto-Konfiguration:

```dotenv
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Empfohlene Registrierung: <https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

## Offizielle Schnittstellen

- RBLighter: <https://apidocs.rh.lighter.xyz/docs/get-started>
- Lighter: <https://apidocs.lighter.xyz/docs/get-started>
- Verwendet werden die offiziellen APIs, kein Browser-Scraping.

## Linux-Download

Das Release enthält ein Linux-x86_64-Binary und benötigt eine kompatible glibc (typischerweise ≥ 2.31).

Geeignete Anbieter sind beispielsweise Vultr, Alibaba Cloud und Tencent Cloud. Preise, Guthaben und Aktionen ändern sich; maßgeblich ist die offizielle Anbieter-Seite.

| Punkt | Empfehlung |
|---|---|
| Betriebssystem | Ubuntu 22.04/24.04 oder gängiges Linux |
| CPU | mindestens 2 Kerne |
| Arbeitsspeicher | mindestens 2 GB |
| Architektur | x86_64 |
| Speicher | mindestens 20 GB |
| Netzwerk | stabil und latenzarm |

```bash
mkdir -p ~/panda-arb-data ~/.config/panda-arb
cd ~/panda-arb-data
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
```

## Externe Konfiguration

`.env`, private Schlüssel und Datenbank werden nicht mitgeliefert. Erstellen Sie `~/.config/panda-arb/.env`:

```dotenv
RBLIGHTER_ACCOUNT_INDEX=0
RBLIGHTER_API_KEY_INDEX=
RBLIGHTER_API_PRIVATE_KEY=
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
LIGHTER_ACCOUNT_INDEX=0
LIGHTER_API_KEY_INDEX=
LIGHTER_API_PRIVATE_KEY=
DRY_RUN=true
LIVE_TRADING_ACK=false
POC_VERIFIED=false
ENABLE_REAL_MARKET_STREAMS=false
```

Schützen Sie die Datei mit `chmod 600 ~/.config/panda-arb/.env`. Ethereum-Adressen sollten für Web3.py im Checksum-Format vorliegen.

## Live-Trading-Checkliste

- Account Index und Markt prüfen;
- veröffentlichte Paar-Mapping-Tabelle beachten;
- Guthaben, Mindestgröße, Genauigkeit und Limits prüfen;
- zuerst mit `DRY_RUN=true` testen;
- `LIVE_TRADING_ACK=true` und `POC_VERIFIED=true` erst nach vollständiger Kontrolle setzen.

## Startbefehle

```bash
./panda-arb-0.1.0-linux-x64-onefile config init --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile config check --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile doctor --network rh_lighter
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser
```

Für die Weboberfläche einen SSH-Tunnel verwenden: `ssh -L 8000:127.0.0.1:8000 user@server`, danach <http://127.0.0.1:8000> öffnen.

## Hintergrundbetrieb

Temporär:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Für dauerhaften Betrieb `systemd` mit automatischem Neustart verwenden und den Port nur lokal bzw. über ein geschütztes Netzwerk freigeben.

Öffentlicher Hintergrundbetrieb ist nur mit Firewall und Zugriffskontrolle zulässig:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 0.0.0.0 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

`kill -9` nur nach erneuter PID-Prüfung verwenden, wenn ein normaler Stopp nicht möglich ist.

## Fehlerbehebung

- **Account Index nicht erlaubt**: Referral-Konto, Zugangsdaten und Konfiguration prüfen.
- **Checksum-Adresse**: Web3.py kann reine Kleinbuchstaben-Adressen zurückweisen; Checksum-Format verwenden.
- **Port belegt**: `ss -ltnp | grep :8000` und nur den Prozess dieses Tools beenden.
- **API nicht erreichbar**: `doctor --network rh_lighter`, DNS, Systemzeit und Firewall prüfen.
- **Order abgelehnt**: Genauigkeit, Mindestgröße, Guthaben, Time-in-Force und Risikolimits prüfen.

Bei einer fehlgeschlagenen Account-Index-Prüfung beendet sich das Programm vor API-Key-Prüfung, Task-Wiederherstellung und Account-Streams. `--env` bestimmt die Konfiguration, `--data-dir` Datenbank und Laufzeitstatus. Beide Verzeichnisse sollten außerhalb des Binaries liegen.

Alternativen Port verwenden:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8001 --no-browser
```

## Paket und Support

Im Paket befinden sich nur Binary und Dokumentation. `.env`, Datenbank und Logs bleiben außerhalb. Für Support niemals private Schlüssel versenden.

Telegram-Community: <https://t.me/+e4p8Vq1ABGthODM1>
