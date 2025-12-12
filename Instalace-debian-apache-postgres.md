---
layout: default
title: Instalace na Debian s Apache a Postges
nav_order: 1
parent: Instalace
---

Níže je popsaný příklad instalace SVJIS na Debian Linux s Apache serverem a Postgres databází.

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}

## Instalace webu SVJ na Debian

Následující příklad nainstaluje SVJIS na server `www.mysvj.cz` a předpokládá se že DNS server je již nakonfigurovaný.

### Nainstalujte aktualizace a utilitu sudo

```
apt-get update
apt-get upgrade
apt-get install sudo
```

### Instalace Postgresql

```
sudo apt-get install postgresql
```

Vytvoření uživatele `svjis_user` a databáze `svjis_db`

```
sudo -u postgres bash
psql
```

```
CREATE USER svjis_user WITH PASSWORD '***';
CREATE DATABASE svjis_db OWNER svjis_user TEMPLATE = 'template0' LC_COLLATE = 'cs_CZ.UTF-8' LC_CTYPE = 'cs_CZ.UTF-8';
```

### Instalace SVJIS

Nainstalujte uv: https://docs.astral.sh/uv/getting-started/installation/

Nainstalujte SVJIS
```
sudo apt-get install git
sudo mkdir /opt/mysvj_cz
cd /opt/mysvj_cz
git clone https://github.com/svjis/svjis2.git
cd svjis2/
```

Vyberte verzi posledního release v2.x.y (https://github.com/svjis/svjis2/releases)
```
git checkout v2.x.y
uv sync --no-dev --group linux-server
source .venv/bin/activate
```

Nastavení lokálních parametrů

```
cd /opt/mysvj_cz/svjis2
touch svjis/svjis/local_settings.py
sudo chgrp -R www-data svjis/
sudo chmod -R g+w svjis/
```

Upravte soubor `local_settings.py`
```
DEBUG = False
SECRET_KEY = '***'
ALLOWED_HOSTS = ['www.mysvj.cz']
CSRF_COOKIE_SECURE = True

TIME_ZONE = 'Europe/Prague'

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        'NAME': 'svjis_db',
        'USER': 'svjis_user',
        'PASSWORD': '***',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}

ADMINS = [('admin', 'admin@mysvj.cz'),]

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.mysvj.cz'
EMAIL_USE_TLS = False
EMAIL_USE_SSL = False
EMAIL_PORT = 25
EMAIL_HOST_USER = 'info@mysvj.cz'
EMAIL_HOST_PASSWORD = '***'
```

Migrace a dokončení nastavení SVJIS

```
cd /opt/mysvj_cz/svjis2/
source .venv/bin/activate
cd svjis/
python manage.py migrate
python manage.py svjis_setup --password <heslo pro uživatele admin>
sudo apt install gettext
python manage.py compilemessages
python manage.py collectstatic
```

### Nastavte Gunicorn

Vytvořte systemd service pro gunicorn:
```
sudo nano /etc/systemd/system/gunicorn-mysvj.service
```

a vložte následující obsah:
```
[Unit]
Description=gunicorn for mysvj.cz
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/opt/mysvj_cz/svjis2/svjis 
ExecStart=/opt/mysvj_cz/svjis2/.venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 svjis.wsgi:application
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

{: .highlight }
Zkontrolujte zda má uživatel `www-data` přístup k pythonu instalovaném pomocí `uv`, který je obvykle ve složce uživatele který `uv` příkaz spouští `~/.local/share/uv/python/`.


Nyní můžete `gunicorn` spustit.

```
sudo systemctl enable gunicorn-mysvj.service
sudo systemctl start gunicorn-mysvj.service
```

To, že `gunicorn` nedodáva statická data jako css, obrázy ... je v pořádku. O statická data se stará `Apache`.

### Nainstalujte Apache server a vytvořte virtuální host mysvj.cz

```
sudo apt-get install apache2
sudo a2enmod proxy proxy_http proxy_balancer lbmethod_byrequests headers
cd /etc/apache2/sites-available
sudo cp 000-default.conf mysvj.cz.conf
sudo a2ensite mysvj.cz.conf
sudo systemctl reload apache2
```

Upravte `mysvj.cz.conf` následujícím způsobem:

```
<VirtualHost *:80>
    ServerAdmin admin@mysvj.cz
    ServerName www.mysvj.cz
    ServerAlias mysvj.cz
    ServerSignature Off
    DocumentRoot /opt/mysvj_cz/www
    <Directory /opt/mysvj_cz/www>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/mysvj_cz_error.log
    CustomLog ${APACHE_LOG_DIR}/mysvj_cz_access.log combined
</VirtualHost>
```

Vytvořte příslušné adresáře a soubory

```
sudo mkdir /opt/mysvj_cz/www
sudo touch /opt/mysvj_cz/www/security.txt
sudo touch /opt/mysvj_cz/www/robots.txt
sudo systemctl reload apache2
```

### Nainstalujte certbot

Je potřeba aby byl DNS server nakonfigurovaný a oba záznamy `www.mysvj.cz` a `mysvj.cz` ukazovaly na náš server.

```
sudo apt-get install certbot
sudo apt-get install python3-certbot-apache
sudo certbot --apache -d www.mysvj.cz -d mysvj.cz
sudo systemctl reload apache2
```

### Dokončete konfiguraci Apache

Upravte soubor `mysvj.cz-le-ssl.conf`

```
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerAdmin admin@mysvj.cz
    ServerName www.mysvj.cz
    ServerAlias mysvj.cz
    ServerSignature Off

    ProxyPreserveHost On
    ProxyRequests Off

    Alias /media /opt/mysvj_cz/svjis2/svjis/media
    Alias /static /opt/mysvj_cz/svjis2/svjis/static
    Alias /robots.txt /opt/mysvj_cz/www/robots.txt
    Alias /.well-known/security.txt /opt/mysvj_cz/www/security.txt
    Alias /favicon.ico /opt/mysvj_cz/www/favicon.ico

    <Directory /opt/mysvj_cz/svjis2/svjis/media>
        Require all granted
    </Directory>

    <Directory /opt/mysvj_cz/svjis2/svjis/static>
        Require all granted
    </Directory>

    <Directory /opt/mysvj_cz/www>
        Require all granted
    </Directory>

    <IfModule mod_headers.c>
        <Directory /opt/mysvj_cz/svjis2/svjis/static/css>
            <FilesMatch "\.css$">
                Header set Cache-Control "max-age=3600, public"
            </FilesMatch>
        </Directory>
    </IfModule>

    <Location "/robots.txt">
        SetHandler None
        Require all granted
    </Location>

    <Location "/.well-known/security.txt">
        SetHandler None
        Require all granted
    </Location>

    RewriteEngine on
    RewriteCond %{HTTP_HOST} ^mysvj.cz$ [NC]
    RewriteRule ^(.*)$ http://www.mysvj.cz$1 [R=301,L]

    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    ProxyPass /media/ !
    ProxyPass /static/ !
    ProxyPass /robots.txt !
    ProxyPass /.well-known/ !
    ProxyPass /favicon.ico !
    ProxyPass        / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
    RequestHeader set X-Forwarded-Proto expr=%{REQUEST_SCHEME}
    RequestHeader set X-Forwarded-For %{REMOTE_ADDR}e

    ErrorLog ${APACHE_LOG_DIR}/mysvj_cz_error.log
    CustomLog ${APACHE_LOG_DIR}/mysvj_cz_access.log combined

Include /etc/letsencrypt/options-ssl-apache.conf
SSLCertificateFile /etc/letsencrypt/live/www.mysvj.cz/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/www.mysvj.cz/privkey.pem

</VirtualHost>
</IfModule>
```

Restart Apache

```
sudo systemctl restart apache2
```

Nyní aplikace běží na adrese https://www.mysvj.cz/ uživatel je admin heslo je vámi dříve zadané heslo. 

### Nastavení odesílání e-mailů

Vytvořte soubor `/opt/mysvj_cz/send_mails.sh`
```
cd /opt/mysvj_cz/svjis2
source .venv/bin/activate
cd svjis
python manage.py svjis_send_messages
```

Nastavení cronu

```
crontab -e
```

Přidejte následující řádek
```
*/5 * * * * bash -c /opt/mysvj_cz/send_mails.sh
```
