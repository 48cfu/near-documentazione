# Introduction to Ledger Nano S
The Ledeger Nano S is one of the most used physical wallet on the market and provides great security. When you own crypto, what you really own is a private key. You need to secure it to secure your funds. Ledger offers the best level of protection: your key remains protected in a certified secure chip [Ledger Nano S](https://shop.ledger.com/products/ledger-nano-s). 

With this tutorial you will learn how to setup and use Ledger with Near Protocol. In case of errors, see the troubleshoot paragraph at the end of this guide.

## Setup a new device or recover a previous one
The first thing you have to is to install the ledger manager. To do this download [Ledger Live](https://www.ledger.com/ledger-live/download/).

![](./immagini/ledger-live.png?raw=true)

Then open Ledger Live on your computer and follow the installation steps. If you already had a Ledger Nano wallet you can restore it.

![](./immagini/ledger-startup.png?raw=true)

Select set up a new device and select Nano S

![](./immagini/ledger-choose-s.png?raw=true)

Set up a PIN code a save the 24-word recovery phrase offline, and not on your computer.

![](./immagini/ledger-pin.png?raw=true)

In case you are unable to complete the security check on ubuntu, restart the application as root and try again
```bash
sudo ./ledger-live-desktop-2.10.0-linux-x86_64.AppImage --no-sandbox
```

## Install `near app` on Ledger Nano S

Now we are going to use Leger Live and install `near app`! The first step you have to do is to `Settings` and enable `Developer mode` in the `Experimental features` section:

![](./immagini/ledger-settings.png?raw=true) ![](./immagini/ledger-dev-mode.png?raw=true)

Now go to `Manager` in the side menu and search for `near` in the App Catalog and install it.

![](./immagini/ledger-installed.png?raw=true)

## Authorize the Ledger Nano S
Open your browser
```bash
sudo -H -u username google-chrome

```
Now access your Near wallet, click on your username and then click on "profile":

![](./immagini/ledger-wallet.png?raw=true)

Afterwards, at the bottom of the page click on `ENABLE` under `Hardware Devices`:

![](./immagini/ledger-hardware-enable.png?raw=true)

It will ask you to connect your Ledger by clicking continue. It is important that when connecting the Near app in Ledger you simultaneously hit the two buttons above once you have entered the Near app (this allows you to skip the initial message and enter the app). In the process always pay attention to the Nano S display.

![](./immagini/ledger-connect.png?raw=true)

When connecting, your Leger will ask you to confirm the key and once confirmed, the following will appear:

![](./immagini/successful-ledger.png?raw=true)

I personally chose to keep existing keys. At the end the following will appear

![](./immagini/ledger-authorized.png?raw=true)

## `near cli` 
From `near cli` run `near login`. Following the straight-forward instructions you can now sign in with the Ledger. You'll have to confirm this on the device. The following message will appear:

`DANGER: THIS GIVES FULL ACCESS TO A DEVICE OTHER THAN LEDGER.`

![](./immagini/ledger-full.png?raw=true)

Now you can stake, unstake, and use other `near cli` commands.

# Troubleshooting
[https://support.ledger.com/hc/en-us/articles/115005165269-Fix-connection-issues](https://support.ledger.com/hc/en-us/articles/115005165269-Fix-connection-issues)
