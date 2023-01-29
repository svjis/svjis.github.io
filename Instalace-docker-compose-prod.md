---
layout: default
title: Docker compose prod
nav_order: 2
parent: Instalace
---

# Docker compose - produkční konfigurace

{: .highlight }
Pokud si chcete aplikaci jen vyzkoušet a nechcete jí publikovat na internetu tak postupujte podle [návodu pro vývojáře](Instalace-docker-compose-dev.md).

## Co budete potřebovat? 

* [Docker Desktop](https://www.docker.com/products/docker-desktop).
* Zaregistrované doménové jméno pro vaší aplikaci.
* Administrátorské přihlášení do vašeho domácího routeru abyste mohli nastavit _port forwarding_ na váš počítač.

## Zprovoznění aplikace

Nejprve si naklonujte projekt [svjis-docker](https://github.com/svjis/svjis-docker). Buď ručně - zelené tlačítko _Code_ a _Dodnload ZIP_, nebo pomocí gitu:

```
git clone https://github.com/svjis/svjis-docker.git
```

{: .important }
V adresáři `docker-compose` je v souborech `svjis-prod.yml` a `create-schema.sh` defaultní heslo k databázi `change-it` - změňte si ho za nové.

V souborech `httpd_conf/httpd.conf` a `httpd_conf/httpd-ssl.conf` je konfigurace serveru Apache, který slouží jako reverzní proxy server a implementuje ssl certifikáty. Najděte v těchto dvou souborech všechny výskyty `svjis.com` a zaměňte je za doménu na které váš server poběží.

Přepněte se do adresáře `docker-compose` a spusťe konfiguraci `svjis-prod.yml`.

```
cd docker-compose
docker-compose -f svjis-prod.yml up -d
```

Nyní se stáhnou docker obrazy pro aplikaci a pro databázi a spustí se v kontajneru. Aplikaci jsme spustili poprvé a tak je ještě potřeba vytvořit databázi. 

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
docker exec svjis_db-prod bash "/firebird/create-schema.sh"
```

Nyní máme vytvořenou prázdnou databázi a aplikace běží na adrese http://localhost:8080. Do aplikace se přihlásíte jménem `admin` a heslem `masterkey`.

{: .warning }
Defaultní heslo admina změňte v __Osobní nastavení - Změna hesla__ ještě před připojením webu na internet.

Zároveň apache poslouchá na http://vase_domena:8181 a na https://vase_domena:443. Nicméně zatím nemáme zprovozněnou doménu ani platný certifikát.

{: .note }
Port 8181 byl zvolen místo standardního portu 80 protože ten nemusí být na daném hostu vždy k dispozici. Port můžete případně změnit v souboru `httpd_conf/httpd.conf` a pak aplikaci restartovat.

## Vypublikování aplikace na internet

Zjistěte si jaká je vaše veřejná IP adresa, třeba zde [https://www.mojeip.cz/](https://www.mojeip.cz/) a ve správě vaší domény si vytvořte A záznam směřující na vaší veřejnou IP adresu (případně CNAME záznam na již existující hostname vaší IP adresy).

Zjistěte si jaká je interní IP adresa vašeho počítače uvnitř vaší sítě, pak se přihlašte do vašeho routeru a zajistěte aby DHCP služba přiřazovala počítači vždy stejnou interní IP adresu. V routeru zároveň nastavte _port forwarding_ tak, aby požadavky na port 80 router přeposílal na _interní_ip_vašeho_počítače_ a port 8181 a požadavky na port 443 přeposílal _interní_ip_vašeho_počítače_ a port 443. Nyní by měla aplikace být dostupná z internetu ale prohlížeč si bude stěžovat na neplatný certifikát.

Zvolte si váš oblíbený certbot který se bude starat o certifikáty - já používám například [getssl](https://github.com/srvrco/getssl). Challange pro ověření domény nasměrujte do adresáře `acme-challenge` a vygenerovaný certifikát a klíč nesměrujte do adresáře `httpd_conf`. Jakmile máte vygenerovaný certifikát tak je potřeba server restartovat.

```
docker-compose -f svjis-prod.yml down
docker-compose -f svjis-prod.yml up -d
```

Nyní by měl být certifikát na adrese `https://<vase.domena>` platný.
