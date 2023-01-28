---
layout: default
title: Docker compose dev
nav_order: 1
parent: Instalace
---

# Docker compose - developerská konfigurace

{: .highlight }
Docker compose je nejjednodušší a nejrychlejší způsob jak SVJIS spustit a vyzkoušet. Co budete potřebovat? Pokud pracujete ve Windows nebo MacOS tak si nainstalujte [Docker Desktop](https://www.docker.com/products/docker-desktop). Pokud pracujete v Linuxu tak si nainstalujte Docker a docker-compose standardním způsobem pro vaší distribuci.

Pokud si chcete aplikaci jen vyzkoušet na vašem počítači, tak pro vás bude nejjednodušší a nejrychlejší developerská konfigurace.

Nejprve si naklonujte projekt `svjis-docker`.

```
git clone https://github.com/svjis/svjis-docker.git
```

{: .important }
V adresáři `docker-compose` je v souborech `svjis-dev.yml` a `create-schema.sh` defaultní heslo `change-it` - změňte si ho za nové.

Přepněte se do adresáře `docker-compose` a spusťe konfiguraci `svjis-dev.yml`.

```
cd docker-compose
docker-compose -f svjis-dev.yml up -d
```

Tím se stáhnou docker obrazy pro aplikaci a pro databázi a spustí se v kontajneru. Aplikaci jsme spustili poprvé a tak je ještě potřeba vytvořit databázi. 

Stáhněte si aktuální databázové schema [database.sql](https://raw.githubusercontent.com/svjis/svjis/master/db_schema/database.sql) a zkopírujte ho do adresáře `docker-compose`. To můžete udělat buď ručně nebo následujícím příkazem.

```
cd docker-compose
curl -k -L -o ./database.sql -L https://raw.githubusercontent.com/svjis/svjis/master/db_schema/database.sql
```

Pak zkopírujte schema `database.sql` a skript `create-schema.sh` do běžícího databázového kontajneru.

```
docker cp ./database.sql svjis_db:/firebird/
docker cp ./create-schema.sh svjis_db:/firebird/
```

Nakonec v kontajneru spusťte skript na vytvoření databáze.

```
docker exec -it svjis_db bash "/firebird/create-schema.sh"
```

Nyní máme vytvořenou prázdnou databázi a aplikace běží na adrese http://localhost:8080. Do aplikace se přihlásíte jménem `admin` a heslem `masterkey`.

Aplikaci zastavíte příkazem

```
docker-compose -f svjis-dev.yml down
```

a opět spustíte

```
docker-compose -f svjis-dev.yml up -d
```

{: .note }
Pokud byste chtěli aplikaci naplnit testovacími daty (uživatelé s různým oprávněním, články, komentáře, tickety od uživatelů...) tak můžete spustit [seleniový test](https://github.com/svjis/svjis-selenium) který testovací data vyrobí. Předpokladem je že máte na počítači nainstalovanou `Javu` a `Maven`.
