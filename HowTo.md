---
layout: default
title: Návody
nav_order: 6
---

# Návody
{: .no_toc }

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}

## Jak doplnit chybějící překlady nebo opravit nesprávné překlady pro můj jazyk

Pokud pro svůj jazyk vidíte chybějící nebo nesprávné překlady, tak se můžete přihlásit do [weblate](https://translate.codeberg.org/engage/svjis/) a překlady doplnit/opravit. Vámi provedené změny budou zapracovány do následující verze aplikace.

## Jak vytvořit lokalizaci pro další jazyk

Seznam aktuálně podporovaných jazyků: 

[![Stav překladu](https://translate.codeberg.org/widget/svjis/multi-auto.svg)](https://translate.codeberg.org/engage/svjis/)

Pro přidání dalšího jazyka je potřeba vytvořit lokalizaci pro nový jazyk - např. pro Slovenštinu:

```
python manage.py makemessages -l sk
```

Otevřít vygenerovaný soubor `svjis/articles/locale/sk/LC_MESSAGES/django.po` a přeložit stringy do slovenštiny. Překlady je možné provádět v aplikaci [weblate](https://translate.codeberg.org/engage/svjis/).

Nakonec zkompilovat překlady

```
python manage.py compilemessages
```

## Jak upgradovat systém na novější verzi

Pro upgradne systému na novější verzi postupujte takto:

Aktivujte virtuální prostředi
```
source .venv/bin/activate
```

Přepněte se do požadované verze
```
git fetch
git checkout v2.x.y
git pull
```

Proveďte upgrade
```
uv sync --no-dev --group linux-server
cd svjis/
python manage.py migrate
python manage.py compilemessages
python manage.py collectstatic
```

Restartujte server
```
sudo systemctl restart gunicorn-mysvj.service
```
