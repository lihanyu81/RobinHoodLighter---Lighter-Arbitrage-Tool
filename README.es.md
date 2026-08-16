<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-1f6feb?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-555?style=for-the-badge" alt="Português"></a>
</p>

##### Sígueme en **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### Servicios de reembolso **BN20 OK40 BG40 GATE60 BYBIT40, disponibles a largo plazo**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### Recarga mediante IA **GPTPRO 130 5X 620 20X 1230 con soporte posventa**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# Herramienta de arbitraje de spreads RobinHood Lighter ↔ Lighter

Sígueme en **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)

## Descripción

Esta herramienta compara el libro de órdenes de RobinHood Lighter (RBLighter) con el de Lighter y ejecuta operaciones de arbitraje cuando el diferencial supera los límites configurados. Las órdenes se envían en ambos mercados y se controlan los fills, errores y posiciones.

> ⚠️ **Riesgo**: el arbitraje no garantiza beneficios. El precio puede cambiar antes de que se llenen las dos órdenes, puede haber slippage, latencia, fallos de red o liquidación. Pruebe siempre con `DRY_RUN=true` y cantidades pequeñas.

## Lógica principal

1. Obtiene los mejores bids/asks de ambos libros.
2. Calcula el spread después de comisiones y slippage.
3. Comprueba tamaño mínimo, precisión, balance y límites de riesgo.
4. Envía una orden maker/taker en cada mercado.
5. Supervisa fills; cancela, reintenta o reduce la posición según la configuración.

## Verificación de referidos de RBLighter

Antes de iniciar el flujo de cuenta, el programa:

1. consulta el Account Index público del invitador;
2. usa las credenciales del invitador para obtener las cuentas invitadas;
3. resuelve todos los Account Index de las billeteras invitadas;
4. comprueba que `RBLIGHTER_ACCOUNT_INDEX` pertenezca al conjunto permitido;
5. rechaza inmediatamente los índices no autorizados;
6. solo después valida la API Key, restaura tareas e inicia el flujo de cuenta.

Si la cuenta de trading es el propio invitador, puede usarse `RBLIGHTER_API_KEY_INDEX` y `RBLIGHTER_API_PRIVATE_KEY`. Para una cuenta invitada, configure además:

```dotenv
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Registro recomendado: <https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

## Interfaces oficiales

- RBLighter: <https://apidocs.rh.lighter.xyz/docs/get-started>
- Lighter: <https://apidocs.lighter.xyz/docs/get-started>
- La aplicación usa las interfaces oficiales; no dependa de scraping del navegador.

## Linux: descarga y preparación

La versión publicada es un binario Linux x86_64. Requiere una distribución con glibc compatible (normalmente glibc ≥ 2.31), acceso a Internet y un servidor con reloj correcto.

Se pueden utilizar servidores de Vultr, Alibaba Cloud o Tencent Cloud. Los precios, créditos y promociones cambian; consulte siempre la página oficial del proveedor.

| Elemento | Recomendación |
|---|---|
| Sistema | Ubuntu 22.04/24.04 u otra distribución Linux común |
| CPU | 2 núcleos o más |
| Memoria | 2 GB o más |
| Arquitectura | x86_64 |
| Disco | 20 GB o más |
| Red | Estable y de baja latencia |

```bash
mkdir -p ~/panda-arb-data ~/.config/panda-arb
cd ~/panda-arb-data
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
```

## Configuración externa

No se incluyen `.env`, claves privadas ni base de datos en el paquete. Cree `~/.config/panda-arb/.env` y cambie los valores de ejemplo:

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

Proteja el archivo: `chmod 600 ~/.config/panda-arb/.env`. Las direcciones Ethereum deben estar en formato checksum cuando las valide Web3.py.

## Operación en vivo

Antes de activar el trading real:

- confirme los índices de cuenta y el mercado correcto;
- use la tabla de pares publicada en el repositorio;
- verifique saldo, tamaño mínimo, precisión y límites;
- pruebe primero con `DRY_RUN=true`;
- ponga `LIVE_TRADING_ACK=true` y `POC_VERIFIED=true` solo después de comprobar la configuración.

## Comandos

```bash
./panda-arb-0.1.0-linux-x64-onefile config init --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile config check --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile doctor --network rh_lighter
./panda-arb-0.1.0-linux-x64-onefile serve \
  --env ~/.config/panda-arb/.env \
  --data-dir ~/panda-arb-data \
  --host 127.0.0.1 --port 8000 --no-browser
```

Abra la interfaz mediante un túnel SSH, por ejemplo `ssh -L 8000:127.0.0.1:8000 usuario@servidor`, y visite <http://127.0.0.1:8000>.

## Ejecución en segundo plano

Para una sesión temporal:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Ejecución pública (solo con firewall y control de acceso configurados):

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 0.0.0.0 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Ver registros y detener el proceso:

```bash
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

Use `kill -9` únicamente después de confirmar el PID y si el proceso no termina normalmente. Para ejecución permanente, se recomienda `systemd` con permisos mínimos.

Para reinicio automático use un servicio `systemd` y restrinja el acceso del puerto a localhost o a una red segura.

## Solución de problemas

- **Account Index no permitido**: compruebe la cuenta del invitador, las credenciales de consulta y el valor configurado.
- **Checksum address**: convierta las direcciones a formato checksum; Web3.py rechaza direcciones solo en minúsculas en ciertos flujos.
- **Puerto ocupado**: `ss -ltnp | grep :8000`; detenga únicamente el PID de esta aplicación.
- **La API no responde**: ejecute `doctor --network rh_lighter`, revise DNS, hora del sistema y firewall.
- **Órdenes rechazadas**: compruebe precisión, tamaño mínimo, balance, time-in-force y límites de riesgo.

Si falla la verificación del Account Index, el programa termina antes de validar la API Key, restaurar tareas o iniciar los flujos de cuenta. `--env` indica el archivo de configuración y `--data-dir` indica la base de datos y el estado de ejecución; manténgalos fuera del ejecutable para conservarlos al actualizar.

Para usar otro puerto:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8001 --no-browser
```

## Contenido y soporte

El paquete contiene únicamente el ejecutable y documentación. La configuración, base de datos y registros deben permanecer fuera del paquete. Para soporte, adjunte mensajes de error y pasos reproducibles, pero nunca claves privadas.

Comunidad de Telegram: <https://t.me/+e4p8Vq1ABGthODM1>
