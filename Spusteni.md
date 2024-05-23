---
layout: default
title: Spuštění
nav_order: 3
---

# Spuštění

Pro s budete potřebovat [Python](https://www.python.org/downloads/) verzi 3.10 nebo vyšší. Aktuální nainstalovanou verzi můžete zkontrolovat příkazem `python --version`.

Naklonujte si projekt a přepněte se do adresáře repozitáře.

```
git clone https://github.com/svjis/svjis2.git
cd svjis2
```

Vytvořte si virtuální prostředí a přepněte se do něj.

```
python -m venv venv
# v Linuxu
source venv/bin/activate
# ve Windows
source venv/Scripts/activate
```

Nainstalujte závislosti.

```
pip install -r requirements.txt
```

Vytvořte databázový model (spuštěním migrací) a základní parametrizaci SVJIS

```
cd svjis
python manage.py migrate
python manage.py svjis_setup
```

Zkompilujte překlady.

{: .highlight }
Abyste mohli zkompilovat překlady, tak budete potřebovat nainstalovanou utilitu `gettext` (vyzkoušejte zda jí máte nainstalovanou `gettext --version`). V prostředí linuxu jí nainstalujete příkazem `sudo apt-get install gettext`, v prostředí Windows je například součástí balíku [git-scm](https://git-scm.com/downloads). Kompilaci překladů můžete také přeskočit a v takovém případě bude aplikace pouze v angličtině.

```
python manage.py compilemessages
```

Aplikaci spustíte příkazem

```
python manage.py runserver
```

Aplikace běží na adrese http://127.0.0.1:8000/ uživatel je admin heslo je masterkey. Heslo změňte v Osobní nastavení - Změna hesla.

Uvedený způsob spuštění je vhodný pro rychlé vyzkoušení aplikace na Vašem počítači. Pokud chcete SVJIS nasadit na server do produkce tak si prostudujte [Django dokumentaci](https://docs.djangoproject.com/en/5.0/howto/deployment/), kde naleznete několik variant jak aplikaci správně spustit v produkčním prostředí.

