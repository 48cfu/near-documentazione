# Tipi di chiavi
Se siete interessati a conoscere ed esplorare la piattaforma NEAR, è necessario capire le diverse tipologie di chiavi:

- chiave del firmatario (i.e. chiave di accesso, chiave dell'account ecc)
- chiave del validatore
- chiave del nodo

Per maggiori dettagli consultate la pagina [near documentation (EN)](https://docs.near.org/docs/validator/keys).


# Accesso al wallet con `near cli`

## Che cose'è `near cli` e come si installa?

NEAR CLI è un'applicazione basata su Node.js che sfrutta la libreria `nearlib` per connettersi alla piattaforma NEAR, generare chiavi sicure ed eseguire transazioni per vostro conto (per contro di chi ha eseguito l'accesso tramite near login).

Per l'installazione è necessaria la versione 10 o superiore di Node.js. Aprite il terminale ed eseguite i seguenti comandi

```bash
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -
sudo apt-get install -y nodejs
```

Verificate l'esito dell'installazione scrivendo `node -v` nel vostro terminale e controllando la versione. Successivamente installate `near cli`

```bash
sudo npm install near-cli -g
```

Se l'installazione dovesse terminare con errori riprovate con `sudo npm install near-cli -g --unsafe-perm`.

## Come creare un account NEAR e autorizzare `near cli`?
Per decidere a quale piattaforma connettersi (betanet, mainnet, guildnet ecc), `near cli` utilizza la variabile d'ambiente `NODE_ENV`. Per esempio per connettersi alla `betanet` ed autorizzare l'accesso al vostro wallet eseguite:

```bash
export NODE_ENV=betanet
near login
```
Vi verrà chiesto se volete partecipare alla raccolta delle informazioni di utilizza a fini statistici.

Se il vostro browser non viene aperto automaticamente, copiate l'indirizzo dal terminale e incollatelo nel vostro browser preferito. Dovreste ora vedere la pagina di registrazione

![](./immagini/nuovo-utente-prima-schermata.png?raw=true) 

Nella schemata successiva scegliete il metodo di recupero del nuovo account

![](./immagini/recovery.png?raw=true) 

Dopo aver configurato il metodo di recupero è il momento di autorizzare `near cli`

![](./immagini/allow.png?raw=true) 

Cliccando su "MORE INFORMATION" vedrete un riepilogo di cosa potete fare con `near cli`

![](./immagini/warning.png?raw=true) 

Confermate l'autorizzazione

![](./immagini/conferma.png?raw=true) 

Alla fine del processo nel terminale apparirà la conferma

![](./immagini/conferma-terminale.png?raw=true) 

# Backup del account

## Dove sono le mie chiavi?

Dopo aver autorizzato `near cli` con successo, le chiavi di accesso all'account vengono generate e salvate in un speciale file `.json` all'interno della cartella:

```bash
~/.near-credentials/betanet
```

Il file  (e.g. `mariorossi.betanet.json`) contiene l'ID dell'account, la chiave pubblica e la corrispondente chiave privata:

```
{"account_id":"mariorossi.betanet","public_key":"ed25519:...","private_key":"ed25519:..."}
```

Eseguite il backup della cartella `~/.near-credentials/betanet` e tenetela al sicuro da occhi indiscreti.


## Ripristino account

Per ripristinare un account è sufficiente copiare la cartella `betanet` dalla vostra location di backup alla cartella `~/.near-credentials/` della home directory dell'utente.

## Come posso delegare i miei NEAR token?

Se volete delegare parte del vostro token a un validator, e.g. `validator_italia_contract`, dal vostro account `mariorossi.betanet` è sufficiente chiamare le funzioni `deposit` e `stake`. Per esempio per delegare 5 NEAR i comandi sono

```bash
near call validator_italia_contract deposit '{}' --accountId mariorossi.betanet --amount 5
near call validator_italia_contract stake '{"amount": "5000000000000000000000000"}' --accountId mariorossi.betanet
```
![](./immagini/deposito-e-stake.png?raw=true)


## Backup chiave del firmatario
Salvate il codice mnemonico durante la creazione dell'account.

## Chiave validator
Viene creato in `~/.near/betanet/validator_key.json`. Potete resettarlo, e rimpiazzarlo dopo aver fermato il vostro nodo validator. 
E' sufficiente fare il backup di questo file.

## Chiave del nodo
Viene creato in `~/.near/betanet/node_key.json` quando create un nuovo nodo. Il backup di questa chiave non è necessaria.
