<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-1f6feb?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### Seguimi su **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### Servizio cashback **BN20 OK40 BG40 GATE60 BYBIT40, disponibile a lungo termine**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### Ricarica AI **GPTPRO 130 5X 620 20X 1230 con assistenza post-vendita**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# Strumento di arbitraggio dello spread RobinHood Lighter ↔ Lighter

Seguimi su **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)

## Descrizione

Lo strumento confronta i book degli ordini di RobinHood Lighter (RBLighter) e Lighter. Quando lo spread, calcolato dopo commissioni e slippage, supera le soglie configurate, invia gli ordini su entrambi i mercati e controlla esecuzioni e posizioni.

> ⚠️ **Rischio**: l’arbitraggio non garantisce profitti. Movimenti di prezzo, slippage, latenza, problemi di rete e liquidazioni possono causare perdite. Testare prima con `DRY_RUN=true` e importi ridotti.

## Logica principale

1. Legge i migliori bid/ask dei due book.
2. Calcola lo spread netto di commissioni e slippage.
3. Verifica quantità minima, precisione, saldo e limiti di rischio.
4. Invia un ordine su ciascun mercato.
5. Monitora i fill; annulla, ritenta o riduce la posizione quando necessario.

## Controllo referral RBLighter

All’avvio il programma consulta l’Account Index pubblico dell’invitante, usa le sue credenziali per recuperare tutti i wallet invitati, risolve i relativi Account Index e controlla `RBLIGHTER_ACCOUNT_INDEX`. Un indice non autorizzato termina immediatamente il programma; solo dopo vengono controllati API Key, task salvati e flussi dell’account.

Per un account invitato configurare:

```dotenv
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Registrazione consigliata: <https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

## API ufficiali

- RBLighter: <https://apidocs.rh.lighter.xyz/docs/get-started>
- Lighter: <https://apidocs.lighter.xyz/docs/get-started>
- Il programma usa le API ufficiali e non il browser scraping.

## Linux: download

La release è un binario Linux x86_64 e richiede una glibc compatibile (in genere ≥ 2.31).

È possibile usare server Vultr, Alibaba Cloud o Tencent Cloud. Prezzi, crediti e promozioni cambiano: fare riferimento alle pagine ufficiali.

| Voce | Consiglio |
|---|---|
| Sistema | Ubuntu 22.04/24.04 o Linux comune |
| CPU | almeno 2 core |
| Memoria | almeno 2 GB |
| Architettura | x86_64 |
| Disco | almeno 20 GB |
| Rete | stabile e a bassa latenza |

```bash
mkdir -p ~/panda-arb-data ~/.config/panda-arb
cd ~/panda-arb-data
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
```

## Configurazione esterna

Il pacchetto non include `.env`, chiavi private o database. Creare `~/.config/panda-arb/.env`:

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

Proteggere il file con `chmod 600 ~/.config/panda-arb/.env`. Gli indirizzi Ethereum devono usare il formato checksum quando vengono verificati da Web3.py.

## Prima del trading reale

- controllare Account Index e mercato;
- consultare la tabella di mapping delle coppie nel repository;
- verificare saldo, quantità minima, precisione e limiti;
- provare prima con `DRY_RUN=true`;
- impostare `LIVE_TRADING_ACK=true` e `POC_VERIFIED=true` solo dopo i controlli.

## Comandi

```bash
./panda-arb-0.1.0-linux-x64-onefile config init --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile config check --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile doctor --network rh_lighter
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser
```

Per l’interfaccia usare un tunnel SSH: `ssh -L 8000:127.0.0.1:8000 utente@server`, quindi aprire <http://127.0.0.1:8000>.

## Esecuzione in background

Sessione temporanea:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Per un servizio persistente usare `systemd` con riavvio automatico e limitare l’accesso alla porta.

Usare il binding pubblico solo dopo aver configurato firewall e controllo degli accessi:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 0.0.0.0 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

Usare `kill -9` solo dopo aver verificato il PID e se l’arresto normale non funziona.

## Risoluzione dei problemi

- **Account Index non autorizzato**: controllare invitante, credenziali referral e configurazione.
- **Indirizzo checksum**: convertire l’indirizzo; Web3.py può rifiutare indirizzi solo minuscoli.
- **Porta occupata**: `ss -ltnp | grep :8000`; terminare solo il PID dell’applicazione.
- **API irraggiungibile**: eseguire `doctor --network rh_lighter` e controllare DNS, orologio e firewall.
- **Ordine rifiutato**: controllare precisione, quantità minima, saldo, time-in-force e limiti.

Se il controllo dell’Account Index fallisce, il programma termina prima di validare API Key, ripristinare task o avviare i flussi dell’account. `--env` indica la configurazione e `--data-dir` database e stato runtime; mantenerli fuori dal binario per conservarli durante gli aggiornamenti.

Per usare un’altra porta:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8001 --no-browser
```

## Pacchetto e supporto

Il pacchetto contiene solo binario e documentazione. Tenere `.env`, database e log all’esterno. Non inviare mai chiavi private al supporto.

Community Telegram: <https://t.me/+e4p8Vq1ABGthODM1>
