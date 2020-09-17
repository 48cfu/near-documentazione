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
    - targets: ['127.0.0.1:9100']

  - job_name: near-exporter
    scrape_interval: 15s
    static_configs:
    - targets: ['127.0.0.1:9333']

  - job_name: near-node
    scrape_interval: 15s
    static_configs:
    - targets: ['127.0.0.1:3030']
  ...
```
### Configurazione Grafana
Dopodiche configurati il file di configurazione in `grafana/custom.ini` con i dati del vostro server di posta:
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
    --network=host \
    -p 9333:9333 \
    near-prometheus-exporter:latest /dist/main -accountId <POOL_ID>
```

## Avvia Prometheus sul tuo server
```bash
sudo docker run -dti \
    --restart always \
    --volume $(pwd)/prometheus:/etc/prometheus/ \
    --name prometheus \
    --network=host \
    -p 9090:9090 prom/prometheus:latest \
    --config.file=/etc/prometheus/prometheus.yml
```

## Avvio Grafana
```bash
sudo chown -R 472:472 grafana/*

sudo docker run -dit \
    --restart always \
    --volume $(pwd)/grafana:/var/lib/grafana \
    --volume $(pwd)/grafana/provisioning:/etc/grafana/provisioning \
    --volume $(pwd)/grafana/custom.ini:/etc/grafana/grafana.ini \
    --user 472 \
    --network=host \
    --name grafana \
    -p 3000:3000 grafana/grafana
```
## Monitoraggio
Aprite il browser all'indizzo IP del vostro server alla porta `3000`:

Username: admin 

Password: admin

Potete modificarli all'interno del file `grafana/custom.ini` e successivamente riavviare il server di Grafana.

![](./immagini/grafana1.png?raw=true) 

![](./immagini/grafana2.png?raw=true) 

## Invio notifiche
Nella sezione notifiche (campana a sinistra dello schermo) cliccare su `Notification channels`. Impostare un nome, e gli indirizzi che ricevaranno la notifca

![](./immagini/grafana3.png?raw=true) 

Dopo aver salvato, aprite la dashboard `Near Node Exporter Full` e cliccate su uno dei contatori (per esempio numero di peers) e impostate il messaggio della vostra notifica.
![](./immagini/grafana4.png?raw=true) 
