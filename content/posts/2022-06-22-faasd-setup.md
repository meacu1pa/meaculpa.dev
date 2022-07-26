---
title: "Run functions on your own terms, no k8s!"
description: "Run OpenFaaS (faasd) on your Ubuntu Server 22.04 VPS"
summary: "Run OpenFaaS (faasd) on your Ubuntu Server 22.04 VPS"
date: 2022-06-22
aliases: []
categories: ["tech"]
tags: ["openfaas", "self-host", "container"]
draft: false
editPost:
    URL: "https://github.com/meacu1pa/meaculpa.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## What is OpenFaas?

> Info: This article is still WIP.

[OpenFaaS](http://openfaas.com) is a cloud agnostic OpenSource project to run functions on your own terms. `faasd` packs the same punch, but you can host it on a single VPS, without all of the `k8s` overhead. What you will get is a fully fledged FaaS engine with queueing out of the box.

Let's start setting it up:

SSH into server, then `sudo apt update && sudo apt upgrade -y`.

## Install Caddy

We will use [Caddy](https://caddyserver.com/) as a reverse proxy, to link to our functions.

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

## Install faasd

```bash
mkdir ~/Downloads && cd ~/Downloads
git clone https://github.com/openfaas/faasd --depth=1
cd faasd
./hack/install.sh
```

### Configure faasd docker-compose.yaml

Change port mapping so that the `faasd gateway` only accepts calls via `localhost`:

`sudo nano /var/lib/faasd/docker-compose.yaml`

```yaml
gateway:
    [...]
    ports:
            -"127.0.0.1:8080:8080"
```

Then restart the faasd service: `sudo systemctl daemon-reload && sudo systemctl restart faasd`

### Enable firewall (optional)

For security reasons, you can enable firewall rules accordingly:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow from 10.62.0.0/16 to 10.62.0.0/16
sudo ufw enable
```

### Obtain your faasd credentials

Store the following output somewhere safe for later reference:

```bash
sudo cat /var/lib/faasd/secrets/basic-auth-password ;echo
```

The associated user for this password is `admin`.

## Configure Caddy as a reverse proxy

Open Caddyfile via `sudo nano /etc/caddy/Caddyfile` and add:

```text
# required for automated lets-encrypt cert issuing & renewal
{
    email you@example.com 
}

faasd.domain.tld {
  reverse_proxy 127.0.0.1:8080
}
```

Reload Caddy via `systemctl reload caddy`.

## Install store functions

### Via web-ui

Visit `faasd.domain.tld` in your browser and login using the credentials obtained above. Then deploy for example 'ASCII cows'. Might take some time after pressing the deploy button.

### Via faas-cli from your local machine

[Install faas-cli](https://docs.openfaas.com/cli/install/) via `curl -sSL https://cli.openfaas.com | sudo -E sh`.

Store your auth pw obtained earlier in a text file called `faasd.txt`.
Then execute following commands on your local machine:

```bash
export OPENFAAS_URL=https://faasd.domain.tld
cat faasd.txt | faas-cli login --username admin --password-stdin
faas-cli store deploy cows
```

The `faas-cli` can also be used to hook up continuous deployments via Gitlab CI/CD or GitHub Actions.

## Use Caddy as a reverse proxy for a specific function

Open Caddyfile via `sudo nano /etc/caddy/Caddyfile` and add:

```text
cows.domain.tld {
    handle_path /* {
        rewrite * /function/cows{path}
        reverse_proxy 127.0.0.1:8080
    }
    reverse_proxy 127.0.0.1:8080 {
    }
}
```

Reload Caddy via `systemctl reload caddy`.

## The End

That's it, you're good to go - happy hacking 👨‍💻!
