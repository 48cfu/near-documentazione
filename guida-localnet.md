
# Continuous Integration e Continuous Deployment del Protocollo NEAR
NEAR è una piattaforma decentralizzata e sicura che gestisce asset di alto valore, come denaro o identità, sufficientemente performante. I contenuti di questa guida:
- Perché automatizzare?
- Continuous Deployment e Continuous Integration
- Test

## Perché automatizzare?
Avviare e mantenere un nodo validatore su una rete blockchain che potenzialmente proteggerà miliardi di euro di valore e altri asset è una grande responsabilità. Se l'infrastruttura dovesse interrompersi o non funzionaree come previsto, è necessario mitigare il rischio di tempi di inattività e accelerare il ripristino.
Questo può essere fatto in molti modi e con vari gradi di sofisticazione. 

## Continuous Deployment e Continuous Integration

Per la guida completa usando GITHUB ACTIONS e SHELL vedi qui: [https://github.com/48cfu/nearcore-automatizzato](https://github.com/48cfu/nearcore-automatizzato).

## Test
Ultimo ma non meno importante è il testing: assicurarsi che l'artefatto che è stato creato dall'origine sia correttamente deployed. Come detto in precedenza, ci possono essere vari livelli di testing e casi limite. Tuttavia qui ci concentreremo su di uno.

### Avvio di una Localnet
Un ottimo modo per testare una nuova versione nearcore è eseguire una rete locale che simula una rete di quattro nodi sulla macchina locale. Ogni nodo verrà generato consecutivamente dalla porta 3030 e la rete contiene quattro nodi. Possiamo quindi verificare che i nodi funzionino correttamente eseguendo il ping di ciascuna delle porte.

Dopo la compilazione di nearcore avviate la localnet per il test

```bash
nearup localnet --binary-path ~/nearcore/target/release/
```

È quindi possibile verificare ciascuno dei nodi sulla rete locale confrontando la versione corrente in esecuzione sulla rete.

Modificare <RETE_PROTOCOLLO_NEAR> (mainnet|testnet|betanet) e <0…3> in base alla propria configurazione

```bash
diff <(curl -s https://rpc.<RETE_PROTOCOLLO_NEAR>.near.org/status | jq .version) <(curl -s http://127.0.0.1:303<0…3>/status | jq .version)
```

Se tutto va bene, ora hai testato la nuova versione del protocollo Near con la tua rete locale.

```bash
nearup stop
nearup <RETE_PROTOCOLLO_NEAR> --binary-path ~/nearcore/target/release/ --nodocker
```
