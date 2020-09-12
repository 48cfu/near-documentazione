
# Continuous Integration e Continuous Deployment su Protocollo NEAR
L'esecuzione di un validatore per supportare una rete non è un lavoro passivo, poiché si afferma che l'automazione del processo aiuta a mitigare i tempi di inattività e ad aumentare la produttività.

Protocollo NEAR : NEAR è una piattaforma applicativa decentralizzata abbastanza sicura da gestire asset di alto valore come denaro o identità e sufficientemente performante da renderli utili per le persone comuni, mettendo il potere dell'Open Web nelle loro mani.
Contenuti
- Perché automatizzare?
- Continuous Deployment
- Continuous Integration
- Test

## Perché automatizzare?
Eseguire un validatore su una rete blockchain che potenzialmente proteggerà miliardi di dollari di valore è una grande responsabilità. Se / quando l'infrastruttura si interrompe o non funziona come previsto, dobbiamo mitigare il rischio di tempi di inattività e accelerare il ripristino.
Questo può essere fatto in molti modi e con vari gradi di sofisticazione. Oggi vedrai come utilizzare i pacchetti creati per rendere più automatizzato il processo di esecuzione di un validatore sulla rete locale.

## Continuous Deployment
Come tutti i software, una blockchain non è diversa, deve essere testata e verificata prima di essere rilasciata in natura. Esistono molte fasi diverse a seconda della rete. NEAR ha tre fasi di test Betanet, Testnet e Mainnet.
- Betanet è il test iniziale in cui le epoche sono più brevi e le cose rischiano di rompersi.
- Testnet è il luogo in cui le epoche sono aumentate per essere le stesse della mainnet e di solito la maggior parte dei problemi iniziali che potresti incontrare in Betanet vengono risolti. Questo per simulare il mainnet il più possibile.
- Mainnet, qui è dove avviene la magia ed è una rete funzionante dal vivo. Non si può tornare indietro (beh forse un hardfork, ma non ne parleremo).
Poiché tutte le impostazioni sono diverse per le diverse esigenze di un validatore o di un operatore di nodo, ci asterremo dal configurare un intero ambiente CD. Tuttavia, mostreremo un pezzo prezioso dell'immagine che può essere modificato in base alle tue esigenze.

### quasi terraform
Pacchetto Terraform per la distribuzione automatica di un validatore sicuro in AWS. Il collegamento sopra è un pacchetto che può essere distribuito localmente per automatizzare la creazione e la sincronizzazione di un validatore sicuro con monitoraggio di Prometheus e Grafana . Il pacchetto include anche un bot che gestisce la posta assegnata al validatore (gli oggetti di scena vanno a eoritu z per il suo bot warchest .
Continuous Integration
Poiché l'unica costante nella vita è il cambiamento, non ci aspettiamo che il nodo o le versioni nearcore siano un software statico e obsoleto. Si evolverà e si adatterà alle esigenze della rete. Dobbiamo essere consapevoli dei cambiamenti e agire di conseguenza. Quando viene rilasciata una nuova versione del nearcore, è ideale per i validatori aggiornare al più presto, questo può essere fatto monitorando quando una nuova versione viene inviata alla rete. È meglio automatizzarlo in modo che una nuova versione non passi inosservata e non venga aggiornata entro un'ora

## Continuous Integration
Poiché l'unica costante nella vita è il cambiamento, non ci aspettiamo che il nodo o le versioni nearcore siano un software statico e obsoleto. Si evolverà e si adatterà alle esigenze della rete. Dobbiamo essere consapevoli dei cambiamenti e agire di conseguenza. Quando viene rilasciata una nuova versione del nearcore, è ideale per i validatori aggiornare al più presto, questo può essere fatto monitorando quando una nuova versione viene inviata alla rete. È meglio automatizzarlo in modo che una nuova versione non passi inosservata e non venga aggiornata entro un'ora


### near-ci
Pacchetto per avere uno script in esecuzione in background in un cron job. Ogni ora lo script controlla se il nodo sta eseguendo la stessa versione della rete che sta convalidando. Si aggiorna in base alle necessità e invia un messaggio di avviso che informa l'operatore se ha avuto esito positivo o negativo.

## Test
Ultimo ma non meno importante è il testing: assicurarsi che l'artefatto che è stato creato dall'origine sia correttamente deployed. Come detto in precedenza, ci possono essere vari livelli di testing e casi limite. Tuttavia qui ci concentreremo su di uno.

### Avvio di una Localnet
Questo è integrato nel pacchetto near-ci discusso sopra. Un ottimo modo per testare una nuova versione nearcore è eseguire una rete locale che simula una rete di quattro nodi sulla macchina locale. Ogni nodo verrà generato consecutivamente dalla porta 3000 e la rete contiene quattro nodi. Possiamo quindi verificare che i nodi funzionino correttamente eseguendo il ping di ciascuna delle porte.

### Ordine delle operazioni
Dopo la compilazione di nearcore

```bash
nearup betanet --binary-path ~/nearcore/target/release/ --nodocker
```

È quindi possibile verificare ciascuno dei nodi sulla rete locale confrontando la versione corrente in esecuzione sulla rete.

Modificare <RETE_PROTOCOLLO_NEAR> (mainnet|testnet|betanet)e <0… 3> in base alla propria configurazione

```bash
diff <(curl -s https://rpc.<NETWORK_YOU_ARE_USING>.near.org/status | jq .version) <(curl -s http://127.0.0.1:303<0……3>/status | jq .version)
```

Se tutto va bene, ora hai testato la nuova versione con la tua rete simulata.
