---
layout: default
title: Návody
nav_order: 6
---

# How to
{: .no_toc }

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}


## Jak vytvořit lokalizaci pro další jazyk

Ve výhozím stavu systém podporuje dva jazyky - Angličtinu a Češtinu. Pro přidání dalšího jazyka je potřeba vytvořit lokalizaci pro nový jazyk - např. pro Slovenštinu:

```
python manage.py makemessages -l sk
```

Otevřít vygenerovaný soubor `svjis/articles/locale/sk/LC_MESSAGES/django.po` a přeložit stringy do slovenštiny.

Nakonec zkompilovat překlady

```
python manage.py compilemessages
```

## Jak upgradovat systém na novější verzi

Pro upgradne systému na novější verzi postupujte takto:

Aktivujte virtuální prostředi
```
source venv/bin/activate
```

Přepněte se do požadované verze
```
git fetch
git checkout v2.x.y
git pull
```

Proveďte upgrade
```
pip install -r requirements.txt
cd svjis/
python manage.py migrate
python manage.py compilemessages
python manage.py collectstatic
```

Restartujte server
```
sudo systemctl restart apache2.service
```