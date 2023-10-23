## Server platform

The following instructions refer to an Ubuntu 22.04 server and should be adapted with other package manager commands if a different distribution is chosen.

## Notes on tools and versions

### Python

CAT is written in [Python](https://www.python.org/). At the moment of this deployment Indico officially supports Python versions 3.9 - 3.11, so CAT is based on Python 3.11.

### Redis

[Redis](https://redis.io/) is used as a communication database for all MEOW tasks. It can be installed in various ways. For simplicity, the official Ubuntu Redis packages are used in MEOW.

### Supervisord

[supervisord](http://supervisord.org) is used to manage the MEOW "service". It takes care of starting the MEOW processes and restart them if needed.

### PDF tools

[qpdf](https://qpdf.sourceforge.io/), [pdftk](https://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/) and  [mupdf](https://mupdf.com) are used by MEOW to manipulate the PDFs for final proceedings.

---

## Installing System Packages

```bash
apt install dbus-broker systemd-container
apt install htop vim p7zip unzip zip git tig
apt install build-essential python3.11-venv python3.11-minimal
apt install qpdf pdftk mupdf-tools mupdf
apt install redis-server redis-tools
```

## Enabling and starting the Redis Service

```bash
systemctl enable redis
systemctl start redis
```

## Setup MEOW code

First, let's create the CAT user and access its working directory

```bash
useradd --comment 'CAT system user' --create-home --system --home-dir /opt/cat --shell /usr/bin/bash cat
su - cat
```

### Downloading sources

```bash
git clone https://github.com/JACoW-org/MEOW.git
```

### Setting Python Virtual Environment

```bash
python3 -m venv venv
. ./venv/bin/activate
./venv/bin/pip install --upgrade pip
./venv/bin/pip install -r requirements.txt
```

### Creating PURR/MEOW Shared Authentication Key

To create a shared authentication key for requests from the PURR client to the MEOW server, you will need an API key. The following command creates an API key for the user `purr`. You can create different keys for different users.

```bash
./venv/bin/python -m meow auth -login purr@indico.jacow.org
```

The output will look like this:

```bash
purr indico.jacow.org <api_key>
```

The API is used in PURR to set up a new user in the [Connection to MEOW](https://purr-docs.jacow.org/Functionalities/connection/)


### Starting supervisord

```bash
./venv/bin/supervisord -n -c ./conf/supervisord.conf
```

## Test that MEOW is up and running

```bash
curl http://127.0.0.1:8080/api/ping/01HBR3MMP09JVYZ7K3ATYPR00X
{
  "method": "ping",
  "params": {
    "user": "purr",
    "host": "indico.jacow.org",
    "date": "2023-10-02T13:51:08.737960"
  }
}
```