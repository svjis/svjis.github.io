---
layout: default
title: Parametrizace
nav_order: 5
---

# Parametrizace
{: .no_toc }

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}

## První kroky

### Nastavení serveru odchozí pošty SMTP

Systém SVJIS při různých událostech používá odesílání emailů, proto je správné nastavení e-mailového rozhraní pro funkci aplikace důležité.

Vytvořte nový soubor `svjis/svjis/local_settings.py` a v něm vytvořte následující konfiguraci.

```
SECRET_KEY = 'produkcni django secret'
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

Nahrajte obrázek domu o rozměrech 330-530 x 94 bodů. 

### Dům

Na stránce dům vyplňte údaje dle katastru nemovitostí.

### Seznam jednotek

Na stránce seznam jednotek vyplňte jednotky tak jak jsou evidované v katastru nemovitostí včetně podílů.

### Seznam uživatelů

Na stránce seznam uživatelů můžete zakládat, editovat a zakazovat uživatele. Uživatele není možné smazat, pouze zakázat. Pokud zakládáte nového uživatele, nevyplníte mu heslo a zaškrtnete _Odeslat přihlášení e-mailem_, tak se mu heslo vygeneruje automaticky a pošle e-mailem (je třeba mít naparametrizovaný _smtp server_).

### Seznam skupin

Na stránce skupin můžete vytvářet a uproavovat práva jednotlivých skupin. V detailu každé skupiny je možné vybrat oprávnění které má skupina povolené.

### Vlastnosti

Zde jsou definované šablony e-mailových zpráva

