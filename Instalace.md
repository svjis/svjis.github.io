---
layout: default
title: Instalace
nav_order: 3
---

# Instalace

Instalaci systému je možné provést několika způsoby:

* Spuštění pomocí docker-compose
* Spuštění v Kubernetes
* Instalací na stávající OS (Linux nebo Windows)

## 1 Spuštění pomocí docker-compose

Docker compose je nejjednodušší a nejrychlejší způsob jak SVJIS spustit a vyzkoušet. Co budete potřebovat?

* Nainstalujte si [Docker Desktop](https://www.docker.com/products/docker-desktop)
* Postupujte dle README v adresáři [docker-compose](https://github.com/svjis/svjis-docker/tree/master/docker-compose).

## 2 Spuštění v Kubernetes

Postupujte dle README v adresáři [kubernetes](https://github.com/svjis/svjis-docker/tree/master/kubernetes).

## 3 Instalace na stávající systém

### 3.1 Databáze Firebird

Stáhněte si z [firebird.org](https://firebirdsql.org/) databázi Firebird 2.5 a nainstalujte jí.
 
Po instalaci v souboru `aliases.conf` založte alias pro produkční a případně i pro testovací databázi.

```
SVJIS_PRODUCTION = C:\Db\SVJIS_PRODUCTION.FDB
SVJIS_TEST = C:\Db\SVJIS_TEST.FDB
```

Změňte defaultní heslo super uživatele sysdba a vytvořte nového uživatele web

```
gsec -user sysdba -password masterkey
modify sysdba -pw ***
add web -pw ***
quit
```

Vytvořte prázdnou databázi `SVJIS_PRODUCTION`

```
isql
Use CONNECT or CREATE DATABASE to specify a database
SQL>CREATE DATABASE 'SVJIS_PRODUCTION' user 'sysdba' password '***' page_size 8192 default character set UTF8;
SQL>QUIT;
```
Spusťte skript `db_schema\database.sql`, který vytvoří všechny databázové objekty. (Skript obsahuje české znaky ve formátu UTF-8).

```
isql -user 'sysdba' -password '***' -input 'db_schema\database.sql' 'localhost:SVJIS_PRODUCTION'
```

Vytvořte novou společnost

```
isql
Use CONNECT or CREATE DATABASE to specify a database
SQL>CONNECT 'localhost:SVJIS_PRODUCTION' user 'sysdba' password '***';
Database:  'localhost:SVJIS_PRODUCTION', User: sysdba
SQL>EXECUTE PROCEDURE SP_CREATE_COMPANY 'Test Company';
SQL>COMMIT;
SQL>QUIT;
```

Nyní je databáze připravená.

### 3.2 Aplikační server Tomcat

Stáhněte si [java development kit](https://www.java.com/) (jdk) a nainstalujte. Nastavte si systémovou proměnou JAVA_HOME na instalaci jdk - např.

```
JAVA_HOME=C:\Program Files\Java\jdk1.8.0_191
```

Stáhněte si [Apache Tomcat](http://tomcat.apache.org/) a nainstalujte. Do souboru `catalina.properties` napište heslo pro databázového uživatele web.

```
db.server=localhost
db.user=web
db.password=***
```

Restartujte Tomcat.

### 3.3 Kompilace, nasazení a spuštění systému

Ujistěte se, že máte nainstalovaný [Maven](https://maven.apache.org/) a [GIT](https://git-scm.com/). Stáhněte si zdrojový kód projektu.

```
git clone https://gitlab.com/svjis/svjis.git
```

Přejděte do adresáře projektu `svjis` a projekt zkompilujte.

```
mvn install
```

V adresáři `target` naleznete binárku `SVJIS-1.5.0-RELEASE.war`. Tu zkopírujte do adresáře Tomcatu `webapps`. Nyní by měl systém běžet na adrese:

```
http://localhost:8080/SVJIS-1.5.0-RELEASE/
```

Pokud systém na uvedené adrese běží, tak ho nyní můžete naparametrizovat.  
