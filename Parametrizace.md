---
layout: default
title: Parametrizace
nav_order: 4
---

# Parametrizace
{: .no_toc }

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}

## První kroky

### Přihlášení a změna výchozího hesla administrátora

Výchozí uživatel je `admin` a heslo je `masterkey`. Jakmile se přihlásíte, tak v menu `Administrace - Uživatelé` změňte heslo.

{: .important }
Výchozí heslo administrátora změňte co nejdříve.

### Nastavení serveru odchozí pošty SMTP

Systém SVJIS při různých událostech používá odesílání emailů, proto je správné nastavení e-mailového rozhraní pro funkci aplikace důležité.

Vytvořte nový soubor `svjis/svjis/local_settings.py` a v něm vytvořte následující konfiguraci.

```
TIME_ZONE = 'Europe/Prague'

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'vas smtp server'
EMAIL_USE_TLS = False
EMAIL_USE_SSL = True
EMAIL_PORT = 465
EMAIL_HOST_USER = 'username k vasemu smtp serveru'
EMAIL_HOST_PASSWORD = 'heslo k vasemu smtp serveru'
```

Odesílání e-mailů se děje na pozadí - systém ukládá e-maily do fronty k odeslání, viz `Administrace - čekající zprávy`. Pro odeslání zprávy je třeba spustit následující příkaz:

```
python manage.py svjis_send_messages
```

Při testování aplikace ho můžete spouštět ručně. Při produkčním nastavení je potřeba nastavit plánovač systému (například cron) aby ho spoštěl v určitých itervalech (například každých 5 minut).

### Založení nového uživatele

V menu `Administrace - Uživatelé` si založte svůj osobní účet.

## Administrace

### Společenství

Na stránce společenství vyplňte adresu a kontaktní údaje vašeho společenství. 

Obrázek v hlavičce webu o rozměrech 940 x 94 bodů. Jako předlohu můžete použít:
* [Obrázek ve formátu PNG](gfx/Header_1.png)
* [Obrázek ve formátu XCF](gfx/Header_1.xcf) (Gimp)

### Dům

Na stránce dům vyplňte údaje dle katastru nemovitostí.

### Seznam jednotek

Na stránce seznam jednotek vyplňte jednotky tak jak jsou evidované v katastru nemovitostí včetně podílů.

### Seznam uživatelů

Na stránce seznam uživatelů můžete zakládat, editovat a zakazovat uživatele. Uživatele není možné smazat, pouze zakázat. Pokud zakládáte nového uživatele, nevyplníte mu heslo a zaškrtnete _Odeslat přihlášení e-mailem_, tak se mu heslo vygeneruje automaticky a pošle e-mailem (je třeba mít naparametrizovaný _smtp server_).

### Seznam skupin

Na stránce skupin můžete vytvářet a uproavovat práva jednotlivých skupin. V detailu každé skupiny je možné vybrat oprávnění které má skupina povolené.

#### Seznam oprávnění

| Oprávnění                    | Popis                                                       |
| ---------------------------- | ----------------------------------------------------------- |
| svjis_add_advert             | Právo vložit inzerát v menu inzeráty                        |
| svjis_view_adverts_menu      | Právo vidět menu inzeráty                                   |
| svjis_edit_article           | Právo editovat články v menu redakce                        |
| svjis_view_redaction_menu    | Právo vidět menu redakce                                    |
| svjis_add_article_comment    | Právo komentovat pod článkem                                |
| svjis_edit_article_menu      | Právo editovat menu v menu redakce                          |
| svjis_edit_admin_building    | Právo editovat informace o buově v menu administrace        |
| svjis_edit_admin_company     | Právo editovat informace o společenství v menu administrace |
| svjis_add_fault_comment      | Právo komentovat v tiketech v menu hlášení závad            |
| svjis_fault_reporter         | Právo vytvářet tikety v menu hlášení závad                  |
| svjis_fault_resolver         | Právo řešit tikety v menu hlášení závad                     |
| svjis_view_fault_menu        | Právo vidět menu hlášení závad                              |
| svjis_edit_article_news      | Právo editovat novinky v menu redakce                       |
| svjis_edit_admin_preferences | Právo editovat preference v menu administrace               |
| svjis_answer_survey          | Právo hlasovat v anketách                                   |
| svjis_edit_survey            | Právo editovat ankety v menu redakce                        |
| svjis_edit_admin_groups      | Právo editovat skupiny v menu administrace                  |
| svjis_edit_admin_users       | Právo editovat uživatele v menu administrace                |
| svjis_view_admin_menu        | Právo vidět menu administrace                               |
| svjis_view_personal_menu     | Právo vidět menu osobní nastavení                           |
| svjis_view_phonelist         | Právo vidět seznam kontaktů v menu kontakty                 |

### Vlastnosti

Zde jsou definované šablony e-mailových zpráva

