# Monitoraggio del nodo NEAR con Prometheus and Grafana
In questa guida imparerai come configurare un `Prometheus node exporter` e un `NEAR node exporter` per esportare le metriche del server NEAR sul server Prometheus e monitorarle con Grafana. Alla fine della guida saremo in grado di ricevere email automatiche di notifica nel caso il server NEAR non dovesse funzionare correttamente. L'email di notifica avrà il seguente aspetto:

![](./immagini/grafana0.png?raw=true) 
## Preliminari
Assicuratevi che tutti i software necessari siano installati (git e docker). In caso contrario eseguite:
```bash
sudo apt-get update
sudo apt install git docker.io -y
```
### Configurazione Prometheus
Dopodiche dobbiamo fare il deployment di [`near prometheus exporter`](https://github.com/masknetgoal634/near-prometheus-exporter) per raccogliere metriche personalizzate dal nodo NEAR utilizzando `json-rpc`
```bash
git clone https://github.com/masknetgoal634/near-prometheus-exporter
cd near-prometheus-exporter
sudo docker build -t near-prometheus-exporter .
```
Ora che abbiamo compilato il "raccoglitore di statische" di NEAR, dobbiamo fare in modo che venga rilevato dal server di Prometheus. Per fare ciò è necessario impostare il node exporter di NEAR come target di Prometheus.

```bash
cd ~/near-prometheus-exporter/etc
```
Apri `prometheus/prometheus.yml` e fa in modificalo in questo modo (Presta molta attenzione agli spazi e tabulazioni).
```yml
  ...
  - job_name: node
    scrape_interval: 5s
    static_configs:
    - targets: ['localhost:9100']

  - job_name: near-exporter
    scrape_interval: 15s
    static_configs:
    - targets: ['localhost:9333']

  - job_name: near-node
    scrape_interval: 15s
    static_configs:
    - targets: ['localhost:3030']
  ...
```
### Configurazione Grafana
Dopodiche configurate il file di configurazione in `grafana/custom.ini` con i dati del vostro server di posta:
```ini
...
#################################### SMTP / Emailing ##########################
[smtp]
enabled = true
host = SMTP SERVER
user = SMTP USERNAME
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = <your__password>
;cert_file =
;key_file =
skip_verify = true
from_address = <your_EMAIL_address>
from_name = Grafana
# EHLO identity in SMTP dialog (defaults to instance_name)
;ehlo_identity = dashboard.example.com
# SMTP startTLS policy (defaults to 'OpportunisticStartTLS') 
;startTLS_policy = NoStartTLS

```
## Esecuzione di Node Exporter sul server
Se utilizzate un firewall, ricordatevi di aprire le porte `3000`, `3030`, `9090`, `9100` e `9333`. Per iniziare eseguiamo il `node exporter` all'interno di un docker container:

```bash
sudo docker run -dit \
    --restart always \
    --volume /proc:/host/proc:ro \
    --volume /sys:/host/sys:ro \
    --volume /:/rootfs:ro \
    --name node-exporter \
    -p 9100:9100 prom/node-exporter:latest \
    --path.procfs=/host/proc \
    --path.sysfs=/host/sys
```

Tenendo presente che il <POOL_ID> è il nome del vostro smart contract, per esemprio `validator_italia_contract`, eseguite:
```bash
sudo docker run -dit \
    --restart always \
    --name near-exporter \
    -p 9333:9333 \
    --user root \
    masknetgoal634/near-prometheus-exporter:latest /dist/main -accountId  validator_italia_contract -url http://<YOUR_IP_ADDRESS>:3030
```

## Avvia Prometheus sul tuo server
```bash
sudo docker run -dti \
    --restart always \
    --volume $(pwd)/prometheus:/etc/prometheus/ \
    --name prometheus \
    -p 9090:9090 prom/prometheus:latest \
    --config.file=/etc/prometheus/prometheus.yml
```

Dopo di che cambiamo l'URL con l'indirizzo IP del nostro server di monitoraggio, lasciamo la porta come prima

sudo nano grafana/provisioning/datasources/all.yml

## Avvio Grafana
```bash
sudo chown -R root:root grafana/*

sudo docker run -dit \
    --restart always \
    --volume $(pwd)/grafana:/var/lib/grafana \
    --volume $(pwd)/grafana/provisioning:/etc/grafana/provisioning \
    --volume $(pwd)/grafana/custom.ini:/etc/grafana/grafana.ini \
    --user root \
    --name grafana \
    -p 3000:3000 grafana/grafana
```
# Aggiunta di un metodo di notifica
Aprite il browser all'indizzo IP del vostro server alla porta `3000`:

Username: admin 

Password: admin

Potete modificarli all'interno del file `grafana/custom.ini` e successivamente riavviare il server di Grafana.

![](./immagini/grafana-first.png?raw=true) 

Aprite la dashboard cliccando su `Near Node Exporter Full`. 

![](./immagini/grafana-roi.png?raw=true) 

Per avere esattamente la stessa dashboard come nell'immagine precedente dovrete importare delle impostazioni aggiuntive [da qui](https://github.com/48cfu/near-documentazione/blob/master/dati/grafana-near-dashboard.json). Cliccate su `Impostazioni` (l'ingranaggio in alto a destra), poi su `<>JSON Model`. Sostituite il contenuto di questo campo con i dati del file [scaricato](https://github.com/48cfu/near-documentazione/blob/master/dati/grafana-near-dashboard.json). Successivamente salvate e tornate nella dashboard.

![](./immagini/grafana-json.png?raw=true) 

## Configurazione delle notifiche
Come prima cosa e' necessario configurare il canale con cui vogliamo ricevere le notifiche. All'inizio di questa guida abbiamo configurati i dati di accesso al server SMPT per permettere a Grafana di inviare notifiche via email. Dunque qui vedremo come configurare le notiche via email. Dal menu' a sinistra aprite la lista di canali di notifiche `Alerting -> Notification channels`. Aggiungete i vostri indirizzi email e salvate.

![](./immagini/grafana-alert-roi.png?raw=true) 

Nella dashboard arrivano gia' preconfigurate 3 tipi notifiche (nella dashboard, o nell'immagine qui sotto):
- Problemi con la produzione tempestiva di blocchi
- In caso di numero di peers basso
- Problemi a ricevere dati dalla rete

![](./immagini/grafana-prenotifiche.png?raw=true) 

Il cuore verde indica che tutto va come previsto. Potete i modificare o disabilitare il criterio di queste notiche a vostro piacimento.

## Esempio aggiunta notifiche 
Se lo desiderate potrete aggiungere altri tipi di notifiche a Grafana. Qui vediamo l'esempio di CPU sotto stress.
Dalla scheda CPU basic nella dashboard clicchiamo su `Edit` di fianco al titolo `CPU Basic`

![](./immagini/grafana-cpubasic.png?raw=true) 

Se cliccate sulla scheda Alert vedrete il seguente messaggio di errore
>Template variables are not supported in alert queries

Questo succedere perche' nella metriche (sotto la scheda Query) abbiamo delle variabili $node, $job e $port. Dovremo sostituire tutte le variabili con il oro valore nelle metriche da notificare.

![](./immagini/grafana-variables.png?raw=true) 

Dopo la modifica, la figa nell'immagine precedere dovra' diventare come:

![](./immagini/grafana-novariables.png?raw=true) 

I valori corretti di queste variabili sono disponible dalla tua dashboard cliccando su `Impostazioni -> Variables` e poi cliccando sulla variabile di interesse. Ora aggiungeremo la notifica sul campo `Busy User` (e' la metrica `B` sotto la scheda `Query`): Cliccare sulla scheda `Alert` poi `Create Alert`.

![](./immagini/grafana-alert-cpu.png?raw=true) 

Impostate il vostro criterio e messaggio di notifica e salvate. Fossimo stati interessati nel campo `Busy System` avremo scelto `query(A, 5m, now)` come condizione di attivazione della notifica. Alla fine un cuore comparira' di fianco al nome `CPU Basic` per indicare che una notifica e' stata configurata.

![](./immagini/grafana-finalcpu.png?raw=true) 