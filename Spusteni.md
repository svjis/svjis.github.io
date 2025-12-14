---
layout: default
title: Spuštění
nav_order: 3
---

# Spuštění

Ověřte, že máte ninstalovaný nástroj pro správu závislostí `uv`:
```
uv --version
```
Pokud ne, tak si ho nainstalujte: https://docs.astral.sh/uv/getting-started/installation/

Naklonujte si projekt a přepněte se do adresáře repozitáře.

```
git clone https://github.com/svjis/svjis2.git
cd svjis2
```

Nainstalujte závislosti.

```
uv sync --no-dev
# v Linuxu
source .venv/bin/activate
# ve Windows
source .venv/Scripts/activate
```

Vytvořte databázový model (spuštěním migrací) a základní parametrizaci SVJIS

```
cd svjis
python manage.py migrate
python manage.py svjis_setup --password <heslo pro uživatele admin>
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

Aplikace běží na adrese http://127.0.0.1:8000/ uživatel je admin heslo je vámi dříve zadané heslo.

{: .highlight }
Uvedený způsob spuštění je vhodný pro rychlé vyzkoušení aplikace na Vašem počítači. Pokud chcete SVJIS nasadit na server do produkce tak si prostudujte [Django dokumentaci](https://docs.djangoproject.com/en/5.0/howto/deployment/), kde naleznete několik variant jak aplikaci správně spustit v produkčním prostředí.

