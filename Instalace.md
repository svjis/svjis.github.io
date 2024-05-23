---
layout: default
title: Instalace
nav_order: 4
---

# Instalace na server

Existuje více způsobů jak nasadit aplikaci na server a různé způsoby jsou popané v [Django dokumentaci](https://docs.djangoproject.com/en/5.0/howto/deployment/). Níže je popsaný příklad instalace SVJIS na Linux Debian s Apache serverem a Postgres databází.

## Příklad instalace na server (Debian, Apache, Postgres)

Následující příklad nainstaluje SVJIS na server `www.mysvj.cz`. Předpokládá se že DNS server je nakonfigurovaný.

### 1. Nainstalujte aktualizace a utilitu sudo

```
apt-get update
apt-get upgrade
apt-get install sudo
```

### 2. Nainstalujte Apache server a vytvořte virtuální host mysvj.cz

```
sudo apt-get install apache2
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

Vytvořte příslušné adresáře

```
sudo mkdir /opt/mysvj_cz
sudo mkdir /opt/mysvj_cz/www
sudo systemctl reload apache2
```

### 3. Nainstalujte certbot

Je potřeba aby byl DNS server nakonfigurovaný a oba záznamy `www.mysvj.cz` a `mysvj.cz` ukazovaly na náš server.

```
sudo apt-get install certbot
sudo apt-get install python3-certbot-apache
sudo certbot --apache -d www.mysvj.cz -d mysvj.cz
sudo systemctl reload apache2
```

### 4. Instalace SVJIS

```
sudo apt-get install git
cd /opt/mysvj_cz
git clone https://github.com/svjis/svjis2.git
cd svjis2/
sudo apt-get install python3.11-venv
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd svjis/
python manage.py migrate
python manage.py svjis_setup
sudo apt install gettext
python manage.py compilemessages
python manage.py collectstatic
sudo apt-get install libapache2-mod-wsgi-py3
```

Nastavení lokálních parametrů

```
touch svjis2/svjis/svjis/local_settings.py
cd svjis2/
sudo chgrp -R www-data svjis/
sudo chmod -R g+w svjis/
```

Upravte soubor `local_settings.py`
```
DEBUG = False
SECRET_KEY = '***'
ALLOWED_HOSTS = ['www.mysvj.cz']

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
EMAIL_PORT = 587
EMAIL_HOST_USER = 'info@mysvj.cz'
EMAIL_HOST_PASSWORD = '***'
```

Upravte soubor `mysvj.cz-le-ssl.conf`

```
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerAdmin admin@mysvj.cz
    ServerName www.mysvj.cz
    ServerAlias mysvj.cz
    ServerSignature Off

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

    <Directory /opt/mysvj_cz/svjis2/svjis/svjis>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

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
    RewriteRule ^(.*)$ http://www.mysvj.cz/$1 [R=301,L]

    WSGIDaemonProcess svjis python-path=/opt/mysvj_cz/svjis2/svjis python-home=/opt/mysvj_cz/svjis2/venv/ lang='en_US.UTF-8' locale='en_US.UTF-8'
    WSGIProcessGroup svjis
    WSGIScriptAlias / /opt/mysvj_cz/svjis2/svjis/svjis/wsgi.py

    ErrorLog ${APACHE_LOG_DIR}/mysvj_cz_error.log
    CustomLog ${APACHE_LOG_DIR}/mysvj_cz_access.log combined

Include /etc/letsencrypt/options-ssl-apache.conf
SSLCertificateFile /etc/letsencrypt/live/www.mysvj.cz/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/www.mysvj.cz/privkey.pem

</VirtualHost>
</IfModule>
```

Reload Apache

```
sudo systemctl reload apache2
```

### 5. Instalace Postgresql

```
sudo systemctl stop apache2
sudo apt-get install postgresql
```

Vytvoření uživatele `svjis_user` a databáze `svjis_db`

```
sudo -u postgres bash
psql
```

```
CREATE USER svjis_user WITH PASSWORD '***';
\l
CREATE DATABASE svjis_db OWNER svjis_user TEMPLATE = 'template0' LC_COLLATE = 'cs_CZ.UTF-8' LC_CTYPE = 'cs_CZ.UTF-8';
```

Instalace ovladače

```
echo "psycopg" > /opt/mysvj_cz/svjis2/local_requirements.txt
pip install -r local_requirements.txt
```

### 6. Nastavení odesílání e-mailů

Vytvořte soubor `/opt/mysvj_cz/send_mails.sh`
```
cd /opt/mysvj_cz/svjis2
source venv/bin/activate
cd svjis
python manage.py svjis_send_messages
```

Nastavení cronu

```
crontab -e
*/5 * * * * bash -c /opt/mysvj_cz/send_mails.sh
```
