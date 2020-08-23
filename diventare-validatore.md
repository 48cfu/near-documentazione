# Smart contract (staking pool) del nodo

Prima di continuare con questa guida, assicuratevi di aver creato un account e di aver dato l'autorizzazione a `near cli` some spiegato [qui](https://github.com/48cfu/near-documentazione/blob/master/chiavi-spiegate.md). Per il resto di questa guida utilizzeremo l'account `mariorossi.betanet`.

Esistono due modi per diventare validatore. La prima è eseguire il deployment di un apposito contratto in un vostro account secondario (spiegate qui di seguito). La seconda modalità è usare uno `staking pool factory` fornito dalla fondazione NEAR (Trovate le istruzioni [qui](https://github.com/48cfu/near-documentazione/blob/master/diventare-validatore-stakehouse.md).

## Settate la variabile d'ambiente
Assicuratevi che la variabile `NODE_ENV` sia settata come segue (betanet|testnet|guildnet|mainnet)

```
export NODE_ENV=betanet
```

## Create un account secondario per lo smart contract

Create un account secondario il cui unico compito sarà contenere lo smart contract del vostro nodo validatore. Per esempio, se l'account secondario è `contrattovalidatore` e il vostro account principale è `mariorossi.betanet`, il comando completo è

```bash
near create-account contrattovalidatore.mariorossi.betanet --masterAccount=mariorossi.betanet
```

In alternativa, è anche possibile impostare il saldo iniziale del account secondario (assicuratevi di avere sufficienti token nell'account principale in quanto serviranno a coprire la commissione di creazione del contratto)

```
near create-account contrattovalidatore.mariorossi.betanet --masterAccount=mariorossi.betanet --initialBalance 40
```

Il vostro nuovo account `contrattovalidatore.mariorossi.betanet` avrà anche egli un file contenente le credenziali `~/.near-credentials/betanet/contrattovalidatore.mariorossi.betanet.json`.

## Create una cartella che conterra' lo smart contract
Qui sotto creiamo la cartella `~/stakewars` e la apriamo
```
mkdir stakewars && cd stakewars
```

## Scaricate lo smart contract
All'interno della cartella `~/stakewars/` eseguite

```
git clone https://github.com/near/initial-contracts && cd initial-contracts/staking-pool
```

## Installate rustup

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

## Compilate lo smart contract

```
rustup target add wasm32-unknown-unknown
./build.sh
```

## Controllate la chiave pubblica del vostro nodo validatore

```
cat ~/.near/betanet/validator_key.json |grep "public_key"
```
Copiatevi questa chiave  "ed25519:XXX",

## Deployment dello smart contract

Specificate il file eseguibile creato dopo la compilazione dello smart contract, e il nome dell'account che conterrà il contratto

```
near deploy --accountId=contrattovalidatore.mariorossi.betanet --wasmFile=res/staking_pool.wasm
```

L'output del predencente comando sarà simile a:

![](./immagini/deployment.png?raw=true)


## Inizializzate lo staking pool

Qui per esempio impostiamo una commissione del 10%, che verrà detratto dai guadagni dei vostri delegatori.

```
near call contrattovalidatore.mariorossi.betanet new '{"owner_id": "mariorossi.betanet", "stake_public_key": "ed25519:XXX", "reward_fee_fraction": {"numerator": 10, "denominator": 100}}' --account_id mariorossi.betanet
```
Dove `ed25519:XXX` è la chiave che avete annotato dal paragrafo "Controllate la chiave pubblica del vostro nodo validatore".

Se non avete sufficienti fondi potreste incontrare l'errore `Error: {"index":0}`. Tuttavia il contratto verrà comunque inizializzato come verificabile all'indirizzo della ricevuta nel terminale

![](./immagini/inizializzazione.png?raw=true)

All'indizzo web della ricevuta vedrete la seguente immagine

![](./immagini/ricevuta-inizializzazione.png?raw=true)

Cliccando su receiver (`contrattovalidatore.mariorossi.betanet`) vi ritroverete davanti il contratto con le relative informazioni (Data dell'inizializzazione, locked/unlocked ecc)

![](./immagini/contratto-nodo.png?raw=true)



## Rendere il contratto permanente
In questa sezione ci assicureremo di rendere il contratto permanente (non sarà possible eseguire un nuovo deployment). Inoltre, rendendo il contratto permanente non ci sarà alcun modo per l'operatore del nodo di sottrarre fondi a eventuale delegatori. Infatti i delegatori delegheranno i loro fondi solamente a nodi con un contratto permanente. Per cominciare controlliamo le chiavi di accesso dell'account secondario. Annotatevi questa chiave, e.g. `ed25519:YYY` 
```bash
near keys contrattovalidatore.mariorossi.betanet | grep public_key
```
Successivamente cancellate la chiave di accesso all'account
```bash

near delete-key --accessKey ed25519:YYY --accountId contrattovalidatore.mariorossi.betanet
```
La conferma sia sel terminale, che nell'explorer di NEAR (ora indica "Locked: Yes" e la conferma di chiave rimossa). NON CANCELLATE LA CHIAVE DI ACCESSO AL VOSTRO ACCOUNT PRINCIPALE, PERDERESTE I VOSTRI TOKEN NEAR PER SEMPRE.

![](./immagini/cancella-chiave.png?raw=true)


![](./immagini/contratto-locked-explorer.png?raw=true)


# Deployment del nodo
Esistono diversi modi per avviare un nodo Near e unirsi alla rete. Il primo è installando `nearup`, il secondo è compilando `nearcore`.

## Con `nearup` (procedura veloce)

1. Aprite la porta: Per far sì che il vostro nodo sia raggiungibile dall'esterno, sarà necessario permettere traffico alla porta TCP `24567` da tutti gli indirizzi IP (`0.0.0.0/0`). Se usate `ufw` potete trovare delle istruzioni [qui](https://help.ubuntu.com/community/UFW).

1. Installate `nearup`
nearup è un tool creato per avviare un nodo Near.

```
# installate tutte le dipendenza
sudo apt update
sudo apt install python3 git curl

# installate nearup
curl --proto '=https' --tlsv1.2 -sSfL https://up.near.dev | python3
```

1. Avviate il nodo con `nearup betanet`. All'avvio inserite il nome del vostro staking pool `contrattovalidatore.mariorossi.betanet`.

## Compilando `nearcore` (procedura lenta)
1. Controllate la versione dell'ultimo release [qui](https://github.com/nearprotocol/nearcore/releases/). Al tempo di scrittura di questa guida, la versione più recente è la `1.11.0-beta.1`. 

1. Compilate `nearcore` dalla sorgente
```
# installate le dipendenze come rustup,clang (piu' dettagli [qui](https://github.com/nearprotocol/nearcore)).


#  build nearcore
cd ~ && git clone https://github.com/nearprotocol/nearcore.git && cd nearcore
git checkout 1.11.0-beta.1
make release

# source ENV
source $HOME/.nearup/env
```

1. Avviate il nodo `nearup betanet --nodocker --binary-path ~/nearcore/target/release`.  All'avvio inserite il nome del vostro staking pool `contrattovalidatore.mariorossi.betanet`.

## Controllo logs

```shell
nearup logs --follow
```


# Delegare

## Deposito e stake
Dopo che il vostro nodo sarà sincronizzato, potrete finalmente creare una proposta di essere parte dei validatori.
Per esempio depositiamo 100 NEAR e poi facciamo la proposata per diventare validator
```bash
near call contrattovalidatore.mariorossi.betanet deposit '{}' --accountId mariorossi.betanet --amount 100
near call contrattovalidatore.mariorossi.betanet stake '{"amount": "100000000000000000000000000"}' --accountId mariorossi.betanet
```

![](./immagini/deposito-e-stake-self.png?raw=true)

## Diventate validatore di Near
In caso di proposta accettata, dopo circa 3 epoch (9 ore in betanet, 36 ore in testnet/mainnet) il vostro nodo diventerà validator (vedrete la lettera V vicino a un numero, e.g. V/96).

![](./immagini/nodo-avviato.png?raw=true)