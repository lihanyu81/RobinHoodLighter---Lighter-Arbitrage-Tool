<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-1f6feb?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### Suivez-moi sur **X (Twitter)** : [@臭臭panda](https://x.com/Chosmos110)
##### Service de cashback **BN20 OK40 BG40 GATE60 BYBIT40, disponible sur le long terme** : [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### Recharge IA **GPTPRO 130 5X 620 20X 1230 avec service après-vente** : [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# Outil d’arbitrage de spread RobinHood Lighter ↔ Lighter

Suivez-moi sur **X (Twitter)** : [@臭臭panda](https://x.com/Chosmos110)

## Présentation

Cet outil compare les carnets d’ordres de RobinHood Lighter (RBLighter) et de Lighter. Lorsque l’écart, après frais et slippage, dépasse les seuils configurés, il envoie des ordres sur les deux marchés et surveille les exécutions.

> ⚠️ **Risque** : l’arbitrage ne garantit aucun bénéfice. Le slippage, la latence, les erreurs réseau et la liquidation peuvent entraîner des pertes. Commencez avec `DRY_RUN=true` et de petites tailles.

## Fonctionnement

1. Récupération des meilleurs bids/asks des deux carnets.
2. Calcul du spread net des frais et du slippage.
3. Vérification de la taille minimale, de la précision, du solde et des limites de risque.
4. Envoi d’un ordre sur chaque marché.
5. Suivi des fills et des positions ; annulation, nouvelle tentative ou réduction si nécessaire.

## Vérification des parrainages RBLighter

Au démarrage, le programme consulte l’Account Index public du parrain, utilise ses identifiants pour récupérer toutes les adresses invitées, résout leurs Account Index, puis vérifie `RBLIGHTER_ACCOUNT_INDEX`. Un index non autorisé provoque un arrêt immédiat. La clé API, la restauration des tâches et les flux de compte ne sont validés qu’après cette étape.

Pour un compte invité, renseignez :

```dotenv
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Inscription recommandée : <https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

## API officielles

- RBLighter : <https://apidocs.rh.lighter.xyz/docs/get-started>
- Lighter : <https://apidocs.lighter.xyz/docs/get-started>
- L’application utilise les API officielles, sans scraping du navigateur.

## Linux : téléchargement

La version publiée est un binaire Linux x86_64 nécessitant une glibc compatible (généralement ≥ 2.31).

Des serveurs Vultr, Alibaba Cloud ou Tencent Cloud peuvent être utilisés. Les prix, crédits et promotions changent ; consultez la page officielle du fournisseur.

| Élément | Recommandation |
|---|---|
| Système | Ubuntu 22.04/24.04 ou Linux courant |
| CPU | 2 cœurs ou plus |
| Mémoire | 2 Go ou plus |
| Architecture | x86_64 |
| Disque | 20 Go ou plus |
| Réseau | stable et faible latence |

```bash
mkdir -p ~/panda-arb-data ~/.config/panda-arb
cd ~/panda-arb-data
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
```

## Configuration externe

Le paquet ne contient ni `.env`, ni clé privée, ni base de données. Créez `~/.config/panda-arb/.env` :

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

Utilisez `chmod 600 ~/.config/panda-arb/.env`. Les adresses Ethereum doivent être au format checksum lorsque Web3.py les valide.

## Avant le trading réel

- confirmer l’Account Index et le bon marché ;
- consulter la table de correspondance des paires du dépôt ;
- vérifier solde, taille minimale, précision et limites ;
- tester d’abord avec `DRY_RUN=true` ;
- activer `LIVE_TRADING_ACK=true` et `POC_VERIFIED=true` uniquement après vérification.

## Commandes

```bash
./panda-arb-0.1.0-linux-x64-onefile config init --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile config check --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile doctor --network rh_lighter
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser
```

Accédez à l’interface avec un tunnel SSH : `ssh -L 8000:127.0.0.1:8000 utilisateur@serveur`, puis ouvrez <http://127.0.0.1:8000>.

## Exécution en arrière-plan

Session temporaire :

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Pour un fonctionnement permanent, utilisez un service `systemd` avec redémarrage automatique et limitez l’accès au port.

N’utilisez une écoute publique qu’avec un pare-feu et un contrôle d’accès configurés :

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 0.0.0.0 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

N’utilisez `kill -9` qu’après vérification du PID et si l’arrêt normal échoue.

## Dépannage

- **Account Index refusé** : vérifier le compte parrain, les identifiants et la configuration.
- **Adresse checksum** : convertir les adresses ; Web3.py peut refuser les adresses uniquement en minuscules.
- **Port occupé** : `ss -ltnp | grep :8000`, puis arrêter uniquement le PID de l’application.
- **API inaccessible** : lancer `doctor --network rh_lighter` et vérifier DNS, horloge et pare-feu.
- **Ordre rejeté** : vérifier précision, taille minimale, solde, time-in-force et limites de risque.

Si le contrôle de l’Account Index échoue, l’application s’arrête avant la validation de l’API Key, la restauration des tâches et les flux du compte. `--env` désigne la configuration et `--data-dir` la base de données et l’état d’exécution ; gardez-les hors du binaire.

Pour choisir un autre port :

```bash
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8001 --no-browser
```

## Paquet et support

Le paquet contient uniquement le binaire et la documentation. Gardez `.env`, la base de données et les journaux à l’extérieur. Ne transmettez jamais de clés privées au support.

Communauté Telegram : <https://t.me/+e4p8Vq1ABGthODM1>
