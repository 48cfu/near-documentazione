
## 1.Deployment nodo validatore NEAR

Controllate le specifiche hardward [qui](https://docs.near.org/docs/roles/validator/hardware). Se usate un firewall sarà necessario permettere il traffico alla porta TCP `24567` da tutti gli indirizzi IP (`0.0.0.0/0`).

Una volta che il vostro server è pronto, installate [nearup](https://github.com/near/nearup). Nearup fornisce un insieme di tool per aiutare i validatori ad avviare il loro nodo, e gli sviluppatori che necessitano di un loro server RPC locale.

Prendetevi del tempo per capire come eseguire l'aggiornamento manuale di [nearcore](https://github.com/nearprotocol/nearcore), e come usare correttamente il comando `nearup betanet --nodocker --binary-path` [qui](https://github.com/48cfu/near-documentazione/blob/master/diventare-validatore.md).

Per connettervi alla rete `betanet` scaricate e compilate la versione più recente di [nearcore release](https://github.com/nearprotocol/nearcore/releases):
- I release di BetaNet sono identificati da tag nel formato  `x.y.z-beta`
- I release di TestNet sono identificati da tag nel formato  `x.y.z-rc`

**Attenzione**, ora dovrete decidere il nome del vostro staking pool!
Se il vostro wallet è `mariorossi.betanet`, dovrete scegliere un nome specifico per il vostro pool, come `mariorossi_staking`. Come vedremo al passo [step 2.2](diventare-validatore-stakehouse.md#22-deploy-your-staking-pool) dove useremo lo staking pool factory, il nome completo del vostro pool diventerà `stakingPool_ID` + `stakehouse.betanet`.

Al primo avvio di  `nearup` vi verrà chiesto il nome del vostro staking pool, e dovrete inserire `mariorossi_staking.stakehouse.betanet` se il nome del vostro pool è `mariorossi_staking`:
```
Enter your account ID (leave empty if not going to be a validator):
```
Dopo l'inserimento del nome, nearup avvierà automaticamente il vostro nodo da validatore con il nome scelto.

Quando nearup avrà concluso il processo di avvio, esportate e salvate la chiave del validatore usando il comando `cat ~/.near/betanet/validator_key.json | grep public_key`, poiché sarà necessario per la configurazione dello smart contract.

## 2.Avvio dello Staking Pool

Ora è necessario collegare il nodo validatore allo smart contract con lo stesso nome. 

### 2.1. Preparazione: unstake di fondi precedentemente lockati

Se eravate un validatore nella rete BetaNet, e state usando il comando `near stake`, seguite le seguenti istruzioni:
1. Eseguite il comando `export NODE_ENV=betanet` nel terminale in modo che near-cli si connetta alla rete `betanet`.
2. Eseguite l'unstake di fondi precedentemente delegati impostando zero come il nuovo importo da delegare: `near stake account_ID <staking public key> 0` - dove la chiave publica e l'ID dell'account sono gli stessi usati inizialmente per eseguire la delegazione.

Ricordatevi che i fondi necessitano di tre epoche per essere disponibile (9 ore in BetaNet). Una volta che i fondi sono disponibili, potrete fermare nearup con il comando `nearup stop`, eseguire la pulizia delle cartella `~/.near/betanet`, e riavviare nuovamente nearcore seguendo il punto 1. di questa guida.

<!-- If you are running an old version of the staking pool jump to [step 3.4](challenge001.md#34-update-an-old-version-of-the-staking-pool-optional) before proceeding. -->

Quando avrete completato questo step, e il vostro nodo non sarà più un validatore potrete continuare con il punto successivo di questa guida.

### 2.2. Deployment dello staking pool

Aggiornamento 22 Luglio 2020: per accogliere e soddifare le specifiche di MainNet Restricted, lo [Staking Pool Factory](https://github.com/near/core-contracts/tree/master/staking-pool-factory) è stato introdotto. Questo smart contract eseguirà il deployment dello staking pool per conto vostro. Inoltre, lo staking pool verrà aggiunto a una speciale lista (_whitelist_) in modo da poter mandare e ricevere token dal vostro wallet. Se avete altre versioni dello staking pool, è consigliabile seguire il punto 2.1 di questa guida per poi continuare con la instruzioni qui sotto.

Lo staking pool factory è un normale smart contract che può essere invocato usando la funzione `call` di near-cli:
```
near call stakehouse.betanet create_staking_pool '{"staking_pool_id":"<POOL_ID>", "owner_id":"<OWNER_ID>", "stake_public_key":"<VALIDATOR_KEY>", "reward_fee_fraction": {"numerator": <X>, "denominator": <Y>}}' --account_id <OWNER_ID> --amount 30 --gas 300000000000000
```
dove:
* `stakehouse.betanet` è lo staking pool factory contract
* `POOL_ID` è il nome dello staking pool per il vostro nodo. Se il nome del vostro wallet è `mariorossi` allora `POOL_ID` dovrà coincidere con `mariorossi.pool.6fb1358`
* `OWNER_ID` è il proprietario del nodo, autorizzato a cambiare la chiave pubblica del nodo e le commissioni
* `VALIDATOR_KEY` è la chiave pubblica `cat ~/.near/betanet/validator_key.json | grep public_key` del nodo 
* `{"numerator": <X>, "denominator": <Y>}` imposta la commissione. Per impostare un 10% di commissione X=10 e Y=100
* `--amount 30` allega 30 $NEAR alla transazione, come fondo per il pagamento del prezzo di creazione del contratto
* `--gas 300000000000000` specifica l'ammontare di gas per la transazioni (opzionale!)

### 2.3. Delegate token allo staking pool

1. Usando near-cli, trasferite fondi dal vostro account principale allo staking pool: `near call stakingPool_ID deposit '{}' --accountId account_ID --amount 100`. 
	Dove 100 è l'ammontare in NEAR che volete trasferire.
1. Eseguite la proposta di diventare uno dei validatori della rete con il comando `near call stakingPool_ID stake '{"amount": "100000000000000000000000000"}' --accountId account_ID`

**Attenzione:** l'importo usato per il deposito nel primo punto è in $NEAR, mentre l'importo usato per la proposta nel secondo punto è in YoctoNEAR. `1` $NEAR è `1*10^24` YoctoNEAR (1 seguito da 24 zeri). Perciò:

| NEAR |  YoctoNEAR  | YoctoNEAR |
| ---- | ----------- | ----------------|
| `1` | `1*10^24` | `1000000000000000000000000` |
| `10` | `1*10^25` | `10000000000000000000000000` |
| `100` | `1*10^26` | `100000000000000000000000000` |
| `1,000` | `1*10^27` | `1000000000000000000000000000` |
| `10,000` | `1*10^28` | `10000000000000000000000000000` |

Vi suggeriamo di prendere confidenza con smart contract dello staking pool (la repository ufficale si trova su [Github](https://github.com/near/core-contracts/tree/master/staking-pool)). Inoltre prestate particolare attenzione alla distinzione tra `stakingPool_ID` e `account_ID`.