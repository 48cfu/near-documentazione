# Introduzione a Ledger Nano S
Il dispositivo Ledeger Nano S è uno dei portafogli fisici più popolari sul mercato. Possedere delle monete virtuali è sinonimo di possedere le chiavi private per l'accesso al portafoglio virtuale. E' dunque necessario mettere al sicuro le vostre chiavi virtuali in modo da proteggere il vostro patrimonio virtuale. Ledger offre uno dei migliori livelli di protezione disponibili: le vostre chiavi sono salvate in uno chip sicuro e certificato [Ledger Nano S](https://shop.ledger.com/products/ledger-nano-s). 

Con questo tutorial imparerete come configurare e usare il dispositvo Ledger Nano S con il vostro wallet Near. In caso di errori seugite le istruzioni a fondo pagina.

## Configurazione di un nuovo dispositvo
Come primo passo installate l'applicazione [Ledger Live](https://www.ledger.com/ledger-live/download/).

![](./immagini/ledger-live.png?raw=true)

Successivamente aprite Ledger Live sul vostro computer e seguite le istruzioni. Se possedete già un Ledger potrete ripristinare l'account.

![](./immagini/ledger-startup.png?raw=true)

Selezionate `Set up a new device` e successivamente `Ledger Nano S`

![](./immagini/ledger-choose-s.png?raw=true)

Scegliete un codice PIN e salvate la frase di recovery di 24 parole nello spazio apposito e non sul computer.

![](./immagini/ledger-pin.png?raw=true)

Se usate ubuntu e rincontrate problemi durante il check di sicurezza riprovate a riavviare l'applicazione con il seguente comando
```bash
sudo ./ledger-live-desktop-2.10.0-linux-x86_64.AppImage --no-sandbox
```

## Installazione `near app` su Ledger Nano S

Andate su `Settings`, poi attiva `Developer mode` nella sezione `Experimental features`:

![](./immagini/ledger-settings.png?raw=true) ![](./immagini/ledger-dev-mode.png?raw=true)

Nel menu a sinistra selezionate `Manager`, cercate e installate `near` dal catalogo di applicazione:

![](./immagini/ledger-installed.png?raw=true)

## Autorizzate Ledger Nano S
Aprite il browser
```bash
sudo -H -u username google-chrome

```
Dal vostro wallet Near wallet, cliccate sul vostro nome utente e poi su `Profile`:

![](./immagini/ledger-wallet.png?raw=true)

Successivamente, cliccate su `ENABLE` nella scheda `Hardware Devices`:

![](./immagini/ledger-hardware-enable.png?raw=true)

Vi verrà chiesto di connettere il vostro dispositivo cliccando su continua. E' importante cliccare sull'app Near e successivamente premere entrambi i tasti per saltare il messaggio indicativo. Prestate molta attenzione allo schermo del dispositivo Nano S durante i passi successivi.

![](./immagini/ledger-connect.png?raw=true)

Durante la connessione, cliccate su conferma sul vostro dispositove Nano S. Successivamente vedrete la seguente schermata:

![](./immagini/successful-ledger.png?raw=true)

Ho deciso di conservare le mie chiavi esistente finché non ho preso dismestichezza con il nuovo Nano S:

![](./immagini/ledger-authorized.png?raw=true)

## `near cli` 
Da `near cli` eseguite `near login`. E' possibile completare il login e autorizzare `near cli` usando il Nano S. Seguite le istruzioni sullo schermo. Inoltre, dovrete confermare il login su Nano S. Il seguente messaggio comparirà:

`DANGER: THIS GIVES FULL ACCESS TO A DEVICE OTHER THAN LEDGER.`

![](./immagini/ledger-full.png?raw=true)

Ora potrete delegare, ritirare le vostre ricompense e altro usando `near cli`. Istruzioni sono [qui](https://github.com/48cfu/near-documentazione/blob/master/diventare-validatore.md).

# Sistemazione errori
[https://support.ledger.com/hc/en-us/articles/115005165269-Fix-connection-issues](https://support.ledger.com/hc/en-us/articles/115005165269-Fix-connection-issues)
