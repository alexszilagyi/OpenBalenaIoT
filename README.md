# OpenBalenaIoT project
### NodeRed, GoogleIoT Core & WiFi Connect

## Setup

1. Clone the repo

2. Login to balena dashboard

3. Compile:

## Compile source code and deploy to dashboard

```
balena build --deviceType raspberry-pi  --arch rpi --emulated

# Create the app if does not exists
balena app create <app-name>

balena deploy <app-name> --source ./
```

> Make sure that the current directory is the one with the cloned repo!

4. Add deployed app to the SD card image

> After downloading the balena os for [Raspberry Pi Zero W](https://www.balena.io/os/#download)

Run the following command:

```
DEBUG=1 balena preload ./image.img --app <app-name> --splash-image logo.png --commit latest
```

> **'image.img'** stands for downloaded image from balenaOS.

## OpenBalena

### Export balena cli
```
export PATH="<path-to-balena-cli>:$PATH"
```

### Use OpenBalena
```
export BALENARC_BALENA_URL=mydomain.com
```

### Download OpenBalena images from stagging server
```
export BALENARC_BALENA_URL=balena-staging.com

balena os download raspberry-pi -o ./raspberry-pi-<version-name>.img --version menu

```
> To download a development image append **.dev** to the version or select from the interactive menu.


## Troubleshooting, debugging

### Fixing SSL certificate
```
Okay, here is the solution for anyone running an old version of OpenBalena:

The problem is that ACMEv1 was deprecated recently, but the cert-provider included in OpenBalena hasn’t been updatd to use ACMEv2.

Here is what I did to fix it. On the Server running OpenBalena:

# Find out the id of the cert-provider container
docker ps
# Run bash in the cert-provider container
docker exec -it <mycontainer> bash
cd /root/.acme.sh
# Get the latest version that will support ACMEv2
./acme.sh --upgrade
# Register a new account on LetsEncrypt
./acme.sh --register-account --server letsencrypt
# Set it as default
./acme.sh --set-default-ca --server letsencrypt
# Find the folder containing your cert and cert config
cd /usr/src/app/certs/api.<your-domain>
# Edit the config file. Only vim is available
vi api.<dour-domain>.conf
Replace all references to https://acme-v01.api.letsencrypt.org with https://acme-v02.api.letsencrypt.org.
Save and exit.
Run the certificate updater (with debug to see that everything is working)

"/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" --debug
```
