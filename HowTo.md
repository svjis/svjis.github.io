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
