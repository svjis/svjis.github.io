---
layout: default
title: Docker compose prod
nav_order: 2
parent: Instalace
---

# Docker compose - produkční konfigurace

{: .highlight }
Docker compose je nejjednodušší a nejrychlejší způsob jak SVJIS spustit a vyzkoušet. Co budete potřebovat? Pokud pracujete ve Windows nebo MacOS tak si nainstalujte [Docker Desktop](https://www.docker.com/products/docker-desktop). Pokud pracujete v Linuxu tak si nainstalujte Docker a docker-compose standardním způsobem pro vaší distribuci.

Pokud jste se rozhodli aplikaci nainstalovat a vystavit pro uživatele na internetu pak budete potřebovat nakonfigurovat doménu na které aplikace poběží a získat pro ní certifikát.


Nejprve si naklonujte projekt `svjis-docker`.

```
git clone https://github.com/svjis/svjis-docker.git
```

{: .important }
V adresáři `docker-compose` je v souborech `svjis-prod.yml` a `create-schema.sh` defaultní heslo `change-it` - změňte si ho za nové.

V souborech `httpd_conf/httpd.conf` a `httpd_conf/httpd-ssl.conf` je konfigurace serveru Apache, který slouží jako reverzní proxy server a implementuje ssl certifikáty. Najděte v těchto souborech všechny výskyty `svjis.com` a zaměňte je za doménu na které váš server poběží.

Přepněte se do adresáře `docker-compose` a spusťe konfiguraci `svjis-prod.yml`.

```
cd docker-compose
docker-compose -f svjis-prod.yml up -d
```

Tím se stáhnou docker obrazy pro aplikaci a pro databázi a spustí se v kontajneru. Aplikaci jsme spustili poprvé a tak je ještě potřeba vytvořit databázi. 

Stáhněte si aktuální databázové schema [database.sql](https://raw.githubusercontent.com/svjis/svjis/master/db_schema/database.sql) a zkopírujte ho do adresáře `docker-compose`. To můžete udělat buď ručně nebo následujícím příkazem.

```
cd docker-compose
curl -k -L -o ./database.sql -L https://raw.githubusercontent.com/svjis/svjis/master/db_schema/database.sql
```

Pak zkopírujte schema `database.sql` a skript `create-schema.sh` do běžícího databázového kontajneru.

```
docker cp ./database.sql svjis_db-prod:/firebird/
docker cp ./create-schema.sh svjis_db-prod:/firebird/
```

Nakonec v kontajneru spusťte skript na vytvoření databáze.

```
docker exec -it svjis_db-prod bash "/firebird/create-schema.sh"
```

Nyní máme vytvořenou prázdnou databázi a aplikace běží na adrese `https://<vase.domena>` ale certifikát není platný. 

Zvolte si váš oblíbený certbot který se bude starat o certifikáty - já používám například [getssl](https://github.com/srvrco/getssl). Challange nasměrujte do adresáře `acme-challenge` a vygenerovaný certifikát a klíč nesměrujte do adresáře `httpd_conf`. Jakmile máte vygenerovaný certifikát tak je potřeba server restartovat.

```
docker-compose -f svjis-dev.yml down
docker-compose -f svjis-dev.yml up -d
```

Nyní by měl být certifikát na adrese `https://<vase.domena>` platný. Do aplikace se přihlásíte jménem `admin` a heslem `masterkey`.
