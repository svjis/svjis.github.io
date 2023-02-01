---
layout: default
title: Parametrizace
nav_order: 4
---

# Parametrizace

{: .highlight }
Aplikace SVJIS umožňuje provozování více společenství v jedné instanci. Systém se nejprve pokusí rozeznat společenství ke kterému přistupujete dle doménového jména v url prohlížeče které porovná s údajem vyplněným v _Administrace - Společenství_. Pokud se systému nepodaří podle domény společenství poznat, tak nabídne seznam všech společenství a nechá vybrat uživatele.

## 1. První kroky

### 1.1 Přihlášení a změna výchozího hesla administrátora

Výchozí uživatel je `admin` a heslo je `masterkey`. Jakmile se přihlásíte, tak v menu `Administrace - Uživatelé` změňte heslo.

{: .important }
Výchozí heslo administrátora změňte co nejdříve.

### 1.2 Založení nového uživatele

V menu `Administrace - Uživatelé` si založte svůj osobní účet.

## 2. Administrace

### 2.1 Společenství

Na stránce společenství vyplňte adresu a kontaktní údaje vašeho společenství. Do pole `Internetová doména` vyplňte doménu na které stránky společenství poběží (např. www.svj-slezska.cz). Systém podporuje více společenství v jedné instanci a podle této domény pak pozná ke kterému společenství přistupujete. 

### 2.2 Dům

Na stránce dům vyplňte údaje dle katastru nemovitostí.

Obrázek v hlavičce webu o rozměrech 940 x 94 bodů. Jako předlohu můžete použít:
* [Obrázek ve formátu PNG](https://raw.githubusercontent.com/svjis/svjis-selenium/master/src/main/resources/Header_1.png)
* [Obrázek ve formátu XCF](https://raw.githubusercontent.com/svjis/svjis-selenium/master/src/main/resources/Header_1.xcf) (Gimp)

### 2.3 Seznam jednotek

Na stránce seznam jednotek vyplňte jednotky tak jak jsou evidované v katastru nemovitostí včetně podílů.

### 2.4 Seznam uživatelů

Na stránce seznam uživatelů můžete zakládat, editovat a zakazovat uživatele. Uživatele není možné smazat, pouze zakázat. Pokud zakládáte nového uživatele, nevyplníte mu heslo a zaškrtnete _Odeslat přihlášení e-mailem_, tak se mu heslo vygeneruje automaticky a pošle e-mailem (je třeba mít ve vlastnostech naparametrizovaný _smtp server_).

#### 2.4.1 Anonymní uživatel

Mezi uživateli je jeden speciální uživatel (anonymní uživatel), který reprezentuje nepřihlášeného uživatele. Tento uživatel by měl být vždy zakázaný aby se na něj nedalo explicitně přihlásit. Anonymní uživatel má přiřazenou speciální roli, na které je možné nastavovat oprávnění a tím řídit co je pro nepřihlášeného uživatele viditelné a co má povolené.  

### 2.5 Seznam rolí

Na stránce rolí můžete vytvářet a uproavovat práva jednotlivých rolí. V detailu každé role je možné vybrat oprávnění které má role povolené.

#### 2.5.1 Seznam oprávnění

| Oprávnění                  | Popis                                       |
| -------------------------- | ------------------------------------------- |
| menu_administration        | Právo vidět menu Administrace               |
| menu_articles              | Právo vidět menu Články                     |
| menu_phone_list            | Právo vidět Seznam kontaktů                 |
| menu_building_units        | Právo vidět menu Jednotky                   |
| menu_personal_settings     | Právo vidět menu Osobní nastavení           |
| menu_redaction             | Právo vidět menu Redakce                    |
| menu_contact               | Právo vidět menu Kontakt                    |
| can_insert_article_comment | Právo psát komentáře pod článkem            |
| can_vote_inquiry           | Právo hlasovat v anketách                   |
| can_write_html             | Právo používat html tagy                    |
| redaction_articles         | Právo vytvářet a upravovat své články       |
| redaction_articles_all     | Právo vytvářet a upravovat všechny články   |
| redaction_mini_news        | Právo vytvářet a upravovat novinky          |
| redaction_inquiry          | Právo vytvářet a upravovat ankety           |
| redaction_menu             | Právo upravovat složky pro články           |
| menu_fault_reporting       | Právo vidět menu Hlášení závad              |
| fault_reporting_reporter   | Právo reportovat nové závady                |
| fault_reporting_resolver   | Právo být řešitelem závad a uzavírat je     |
| fault_reporting_comment    | Právo vkládat komentáře k závadám           |
| menu_adverts               | Právo vidět menu Inzeráty                   |
| can_insert_advert          | Právo vkládat nové inzeráty                 |

### 2.6 Vlastnosti

Zde jsou definované gloální parametry pro společenství

| Klíč                                     | Popis                                                                                 |
| ---------------------------------------- | ------------------------------------------------------------------------------------- |
| advert.page.size                         | Počet inzerátů na stránce.                                                            |
| anonymous.user.id                        | Id nepřihlášeného uživatele.                                                          |
| article.menu.default.item                | Id výchozí složky článků. (0 = žádné výchozí menu)                                    |
| article.page.size                        | Počet článků na stránce.                                                              |
| article.top.months                       | Maximální historie položek v boxu "Nejčtenější články".                               |
| article.top.size                         | Počet položek v boxu "Nejčtenější články".                                            |
| error.report.recipient                   | E-Mail příjemce reportu o chybách aplikace.                                           |
| faults.page.size                         | Počet hlášení závad na stránce.                                                       |
| google.analytics.id                      | Google analytics Id.                                                                  |
| http.meta.description                    | Http meta popis. (používají vyhledávače)                                              |
| http.meta.keywords                       | Http meta klíčová slova. (používají vyhledávače)                                      |
| mail.login                               | Login k účtu odchozí pošty smtp.                                                      |
| mail.password                            | Heslo k účtu odchozí pošty smtp.                                                      |
| mail.sender                              | E-Mail, který bude uveden jako odesílající ve všech notifikacích posílaných systémem. |
| mail.smtp                                | Server odchozí pošty smtp.                                                            |
| mail.smtp.port                           | Port serveru odchozí pošty smtp.                                                      |
| mail.smtp.ssl                            | SSL/TLS protokol serveru odchozí pošty. (true/false)                                  |
| mail.template.article.notification       | Šablona notifikace pro upozornění na nový článek.                                     |
| mail.template.comment.notification       | Šablona notifikace pro upozornění na nový komentář pod článkem.                       |
| mail.template.fault.assigned             | Šablona notifikace pro upozornění na přiřazení tiketu řešiteli.                       |
| mail.template.fault.closed               | Šablona notifikace pro upozornění na uzavření tiketu.                                 |
| mail.template.fault.comment.notification | Šablona notifikace pro upozornění na nový komentář pod nahlášenou závadou.            |
| mail.template.fault.notification         | Šablona notifikace pro upozornění na novou nahlášnou závadu.                          |
| mail.template.fault.reopened             | Šablona notifikace pro upozornění na znovu otevření tiketu.                           |
| mail.template.lost.password              | Šablona pro zaslání resetovaného hesla.                                               |
| permanent.login.hours                    | Maximální doba trvání přihlášení v hodinách.                                          |

## 3. Lokalizace

Ve výhozím stavu systém podporuje dva jazyky - Angličtinu a Češtinu. Pro přidání dalšího jazyka je potřeba přidat nový jazyk do tabulky `LANGUAGE` a doplnit pro něj překlady v tabulce `LANGUAGE_DICTIONARY`.

{: .note }
Pokud byste chtěli aplikaci lokalizovat do dalšího jazyka tak prozkoumejte soubor [database.sql](https://github.com/svjis/svjis/blob/4b566e165bc2f13a6191babd770a750af5e5aa95/db_schema/database.sql#L1617). Případný pull request je vítaný.
