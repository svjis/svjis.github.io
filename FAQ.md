---
layout: default
title: Často kladené dotazy
nav_order: 5
---

# Často kladené dotazy

## Chtěl bych zprovoznit Google Analytics

Zprovoznění a nastavení služby Google Analytics proveďte následovně:

* Zaregistrujte si službu Google Analytics pro vaší doménu a získejte její id. Například `UA-1234567-1`.
* Přejděte do Administrace - Vlastnosti a nastavte vlastnost google.analytics.id na id, které jste získali.

## Jak vložím obrázek do článku?

Vložení obrázku do textu se provede následovně:

* Obrázek (obrázky) připojte k článku jako přílohu
* Do textu, kde má být obrázek umístěn, napište jeho název ve složených závorkách. Například takto `{foto1.jpg}`

## Lze na jedné instalaci provozovat více SVJ?

Ano. Pro vytvoření dalšího SVJ se přihlašte do databáze a spusťte proceduru `EXECUTE PROCEDURE SP_CREATE_COMPANY 'New Company'; COMMIT;`.
Pokud máte instalaci v `docker-compose` tak se připojte do databázového kontajneru následujícím způsobem:

```
docker exec -it svjis_db bash
/usr/local/firebird/bin/isql -user "sysdba" -password "heslo do databáze" "localhost:SVJIS_PRODUCTION"
sql> EXECUTE PROCEDURE SP_CREATE_COMPANY 'New Company';
sql> COMMIT;
sql> quit;
```

Počet SVJ běžících na jedné instanci není omezen.

# Diskuze

Pro další dotazy a odpovědi navštivte [diskuzi](https://github.com/orgs/svjis/discussions) na GitHubu. 
