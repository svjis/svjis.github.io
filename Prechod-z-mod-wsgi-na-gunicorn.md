---
layout: default
title: Přechod z mod_wsgi na gunicorn
nav_order: 3
parent: Instalace
---

Níže je popsaný přechod z `mod_wsgi` na `gunicorn`.

Návody instalace popsané v předchozích kapitolách používjí `mod_wsgi`, ten je ale závislý na systémové verzi pythonu a bez překompilace neumí použít jinou verzi. Řešením je přechod na `gunicorn`, který je popsaný v této kapitole.

Tato konfigurace umožní, aby se o správnou verzi pythonu staral `uv`.

Upozornění: `gunicorn` neběží na Windows.

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}

## Instalace gunicorn

Nainstalujte gunicorn:

```
cd /opt/mysvj_cz/svjis2/
uv sync --no-dev --group linux-server
```

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
ExecStart=/opt/mysvj_cz/svjis2/.venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 mysvj.wsgi:application
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Zkontrolujte zda má uživatel `www-data` přístup k pythonu instalovaném pomocí `uv`, který je obvykle ve složce uživatele který `uv` příkaz spouští `~/.local/share/uv/python/`.


Nyní můžete `gunicorn` spustit.

```
sudo systemctl enable gunicorn-mysvj.service
sudo systemctl start gunicorn-mysvj.service
```

To, že `gunicorn` nedodáva statická data jako css, obrázy ... je v pořádku. O statická data se stará `Apache`.

## Změna konfigurace Apache

Povolte proxy moduly:

```
sudo a2enmod proxy proxy_http proxy_balancer lbmethod_byrequests headers
```

Upravte soubor `/etc/apache2/sites-available/mysvj.cz-le-ssl.conf`

Přidejte `ProxyPreserveHost On` a `ProxyRequests Off` na začátek souboru:

```
ServerAdmin admin@mysvj.cz
ServerName www.mysvj.cz
ServerAlias mysvj.cz
ServerSignature Off

ProxyPreserveHost On
ProxyRequests Off
```

Zakomentujte následující wsgi adresář

```
# <Directory /opt/mysvj_cz/svjis2/svjis/svjis>
#     <Files wsgi.py>
#         Require all granted
#     </Files>
# </Directory>
```

Zakomentujte konfiguraci pro `mod_wsgi` a vložte konfiguraci pro proxy-path:

```
# WSGIDaemonProcess svjis python-path=/opt/mysvj_cz/svjis2/svjis python-home=/opt/mysvj_cz/svjis2/.venv/ lang='en_US.UTF-8' locale='en_US.UTF-8'
# WSGIProcessGroup svjis
# WSGIScriptAlias / /opt/mysvj_cz/svjis2/svjis/svjis/wsgi.py

ProxyPass /media/ !
ProxyPass /static/ !
ProxyPass /robots.txt !
ProxyPass /.well-known/ !
ProxyPass /favicon.ico !
ProxyPass        / http://127.0.0.1:8000/
ProxyPassReverse / http://127.0.0.1:8000/
ProxyPassMatch ^/(media/.*) !
RequestHeader set X-Forwarded-Proto expr=%{REQUEST_SCHEME}
RequestHeader set X-Forwarded-For %{REMOTE_ADDR}e
```

Restartujte Apache
```
sudo systemctl restart apache2
```
