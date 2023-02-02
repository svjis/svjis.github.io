---
layout: default
title: How To
nav_order: 5
---

# How to
{: .no_toc }

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}


## Jak vytvořit zálohu databáze

Vytvořte v kontajneru zálohu do souboru `SVJIS_PRODUCTION_current.fbk` a zkopírujte jí na lokální počítač.

```
docker exec svjis_db-prod bash -c "/usr/local/firebird/bin/gbak -B localhost:SVJIS_PRODUCTION /firebird/SVJIS_PRODUCTION_current.fbk -USER <db_user> -PASSWORD <db_password>"
docker cp svjis_db-prod:/firebird/SVJIS_PRODUCTION_current.fbk ./
```

## Jak obnovit databázi ze zálohy

Obnovu databáze je potřeba udělat při odpojené aplikaci. Postupujte podle následujících kroků.

1. zastavte aplikaci `docker-compose -f svjis-prod.yml down`
1. v souboru `svjis-prod.yml` zakomentujte konfiguraci pro `svjis-app`
1. spusťte nyní pouze databázi `docker-compose -f svjis-prod.yml up -d`
1. zkopírujte zálohu z lokálního počítače do db kontajneru `docker cp ./SVJIS_PRODUCTION_current.fbk svjis_db-prod:/firebird/`
1. spusťte restore `docker exec svjis_db-prod bash -c "/usr/local/firebird/bin/gbak -R /firebird/SVJIS_PRODUCTION_current.fbk SVJIS_PRODUCTION -REP -USER <db_user> -PASSWORD <db_password>"`
1. zastavte databázi `docker-compose -f svjis-prod.yml down`
1. odkomentujte `svjis-app` v souboru `svjis-prod.yml`
1. nakonec spusťte aplikaci `docker-compose -f svjis-prod.yml up -d`


## Jak na dané instanci vytvořit další společenství

Pro vytvoření dalšího SVJ se přihlašte do databáze a spusťte proceduru `EXECUTE PROCEDURE SP_CREATE_COMPANY 'New Company'; COMMIT;`.
Pokud máte instalaci v `docker-compose` tak se připojte do databázového kontajneru následujícím způsobem:

```
docker exec -it svjis_db bash
/usr/local/firebird/bin/isql -user "sysdba" -password "heslo do databáze" "localhost:SVJIS_PRODUCTION"
sql> EXECUTE PROCEDURE SP_CREATE_COMPANY 'New Company';
sql> COMMIT;
sql> quit;
```

Počet SVJ běžících na jedné instanci není omezen.
