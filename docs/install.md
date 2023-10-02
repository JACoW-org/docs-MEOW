## Installing System Packages

```bash
apt install dbus-broker systemd-container
apt install htop vim p7zip unzip zip git tig
apt install build-essential python3.11-venv python3.11-minimal
apt install qpdf pdftk mupdf-tools mupdf
apt install redis-server redis-tools
```

## Enabling Redis Service

```bash
systemctl enable redis
```

## Starting Redis Service

```bash
systemctl start redis
```

## Creating CAT user

```bash
useradd --comment 'CAT system user' --create-home --system --home-dir /opt/cat --shell /usr/bin/bash cat
```

# Access to user home

```bash
su - cat
```

# Downloading sources

```bash
git clone https://github.com/JACoW-org/MEOW.git
```

# Setting Python Virtual Environment

```bash
python3 -m venv venv
. ./venv/bin/activate
./venv/bin/pip install --upgrade pip
./venv/bin/pip install -r requirements.txt
```

# Creating Authentication Key

```bash
./venv/bin/python -m meow auth -login purr@indico.jacow.org
purr indico.jacow.org 01HBR3MMP09JVYZ7K3ATYPR00X
```

# Starting supervisord

```bash
./venv/bin/supervisord -n -c ./conf/supervisord.conf
```

# Ping Test

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