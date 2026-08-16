<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/简体中文-中文-555?style=for-the-badge" alt="简体中文"></a>
  <a href="README.en.md"><img src="https://img.shields.io/badge/EN-English-555?style=for-the-badge" alt="English"></a>
  <a href="README.ja.md"><img src="https://img.shields.io/badge/日本語-日本語-555?style=for-the-badge" alt="日本語"></a>
  <a href="README.ko.md"><img src="https://img.shields.io/badge/한국어-한국어-555?style=for-the-badge" alt="한국어"></a>
  <a href="README.es.md"><img src="https://img.shields.io/badge/ES-Español-555?style=for-the-badge" alt="Español"></a>
  <a href="README.de.md"><img src="https://img.shields.io/badge/DE-Deutsch-555?style=for-the-badge" alt="Deutsch"></a>
  <a href="README.fr.md"><img src="https://img.shields.io/badge/FR-Français-555?style=for-the-badge" alt="Français"></a>
  <a href="README.it.md"><img src="https://img.shields.io/badge/IT-Italiano-555?style=for-the-badge" alt="Italiano"></a>
  <a href="README.pt.md"><img src="https://img.shields.io/badge/PT-Português-1f6feb?style=for-the-badge" alt="Português"></a>
</p>

##### Siga-me no **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)
##### Serviço de cashback **BN20 OK40 BG40 GATE60 BYBIT40, disponível a longo prazo**: [@熊猫寨返佣机器人](https://t.me/RebateGobot)
##### Recarga por IA **GPTPRO 130 5X 620 20X 1230 com suporte pós-venda**: [@熊猫寨自营业务](https://service.pandazhai.com/products)

---

# Ferramenta de arbitragem de spread RobinHood Lighter ↔ Lighter

Siga-me no **X (Twitter)**: [@臭臭panda](https://x.com/Chosmos110)

## Visão geral

Esta ferramenta compara os livros de ordens do RobinHood Lighter (RBLighter) e do Lighter. Quando o spread, já descontadas taxas e slippage, ultrapassa os limites configurados, ela envia ordens nos dois mercados e acompanha fills e posições.

> ⚠️ **Risco**: arbitragem não garante lucro. Variação de preço, slippage, latência, falhas de rede e liquidação podem gerar perdas. Comece sempre com `DRY_RUN=true` e valores pequenos.

## Lógica principal

1. Obtém os melhores bids/asks dos dois livros.
2. Calcula o spread líquido de taxas e slippage.
3. Verifica tamanho mínimo, precisão, saldo e limites de risco.
4. Envia uma ordem em cada mercado.
5. Monitora fills; cancela, tenta novamente ou reduz a posição quando necessário.

## Verificação de indicação do RBLighter

Na inicialização, o programa consulta o Account Index público do convidador, usa as credenciais dele para obter todas as carteiras convidadas, resolve seus Account Indexes e verifica `RBLIGHTER_ACCOUNT_INDEX`. Um índice não autorizado encerra imediatamente o programa; somente depois são validados API Key, tarefas salvas e fluxos da conta.

Para uma conta convidada, configure:

```dotenv
RBLIGHTER_REFERRAL_API_KEY_INDEX=
RBLIGHTER_REFERRAL_API_PRIVATE_KEY=
```

Cadastro recomendado: <https://robinhoodchain.lighter.xyz/?referral=PANDAZHAI>

## APIs oficiais

- RBLighter: <https://apidocs.rh.lighter.xyz/docs/get-started>
- Lighter: <https://apidocs.lighter.xyz/docs/get-started>
- O programa usa as APIs oficiais, sem scraping do navegador.

## Linux: download

A versão publicada é um binário Linux x86_64 e requer glibc compatível (normalmente ≥ 2.31).

Você pode usar servidores da Vultr, Alibaba Cloud ou Tencent Cloud. Preços, créditos e promoções mudam; consulte sempre a página oficial do provedor.

| Item | Recomendação |
|---|---|
| Sistema | Ubuntu 22.04/24.04 ou Linux comum |
| CPU | 2 núcleos ou mais |
| Memória | 2 GB ou mais |
| Arquitetura | x86_64 |
| Disco | 20 GB ou mais |
| Rede | estável e de baixa latência |

```bash
mkdir -p ~/panda-arb-data ~/.config/panda-arb
cd ~/panda-arb-data
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile
wget https://github.com/lihanyu81/RobinHoodLighter---Lighter-Arbitrage-Tool/raw/main/panda-arb-0.1.0-linux-x64-onefile.sha256
sha256sum -c panda-arb-0.1.0-linux-x64-onefile.sha256
chmod +x panda-arb-0.1.0-linux-x64-onefile
```

## Configuração externa

O pacote não inclui `.env`, chaves privadas nem banco de dados. Crie `~/.config/panda-arb/.env`:

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

Proteja o arquivo com `chmod 600 ~/.config/panda-arb/.env`. Endereços Ethereum devem estar no formato checksum quando validados pelo Web3.py.

## Antes do trading real

- confirme o Account Index e o mercado;
- consulte a tabela de mapeamento de pares do repositório;
- verifique saldo, tamanho mínimo, precisão e limites;
- teste primeiro com `DRY_RUN=true`;
- ative `LIVE_TRADING_ACK=true` e `POC_VERIFIED=true` somente após revisar tudo.

## Comandos

```bash
./panda-arb-0.1.0-linux-x64-onefile config init --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile config check --env ~/.config/panda-arb/.env
./panda-arb-0.1.0-linux-x64-onefile doctor --network rh_lighter
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser
```

Use um túnel SSH para abrir a interface: `ssh -L 8000:127.0.0.1:8000 usuário@servidor`, depois acesse <http://127.0.0.1:8000>.

## Execução em segundo plano

Sessão temporária:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
```

Para execução permanente, use um serviço `systemd` com reinício automático e restrinja o acesso à porta.

Use o binding público somente depois de configurar firewall e controle de acesso:

```bash
nohup ./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 0.0.0.0 --port 8000 --no-browser > ~/panda-arb-data/panda-arb.log 2>&1 &
echo $! > ~/panda-arb-data/panda-arb.pid
tail -f ~/panda-arb-data/panda-arb.log
kill "$(cat ~/panda-arb-data/panda-arb.pid)"
```

Use `kill -9` somente após confirmar o PID e quando o encerramento normal falhar.

## Solução de problemas

- **Account Index não autorizado**: verifique convidador, credenciais de indicação e configuração.
- **Endereço checksum**: converta o endereço; o Web3.py pode rejeitar endereços somente em minúsculas.
- **Porta ocupada**: `ss -ltnp | grep :8000`; encerre somente o PID desta aplicação.
- **API inacessível**: execute `doctor --network rh_lighter` e verifique DNS, horário e firewall.
- **Ordem rejeitada**: confira precisão, tamanho mínimo, saldo, time-in-force e limites de risco.

Se a verificação do Account Index falhar, o programa termina antes de validar a API Key, restaurar tarefas ou iniciar os fluxos da conta. `--env` define a configuração e `--data-dir` define banco de dados e estado de execução; mantenha ambos fora do binário para preservá-los durante atualizações.

Para usar outra porta:

```bash
./panda-arb-0.1.0-linux-x64-onefile serve --env ~/.config/panda-arb/.env --data-dir ~/panda-arb-data --host 127.0.0.1 --port 8001 --no-browser
```

## Pacote e suporte

O pacote contém apenas o binário e a documentação. Mantenha `.env`, banco de dados e logs fora do pacote. Nunca envie chaves privadas ao suporte.

Comunidade do Telegram: <https://t.me/+e4p8Vq1ABGthODM1>
