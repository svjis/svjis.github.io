---
layout: default
title: Často kladené dotazy
nav_order: 7
---

# Často kladené dotazy
{: .no_toc }

{: .highlight }
Své dotazy můžete nově pokládat v [diskuzích na GitHubu](https://github.com/orgs/svjis/discussions).

## Obsah
{: .no_toc .text-delta }

1. TOC
{:toc}


## Jak vložím obrázek do článku?

Vložení obrázku do textu se provede následovně:

* Obrázek (obrázky) připojte k článku jako přílohu
* Do textu, kde má být obrázek umístěn, napište jeho název ve složených závorkách. Například takto `{foto1.jpg}`

## Jak nastavím aplikaci aby byla v angličtině?

Jazyk lokalizace se volí automaticky podle nastavení v prohlížeči uživatele. Pokud se Vám tedy zobrazuje česká lokalizace a vy preferujete například anglickou, tak v menu Osobní nastavení - Nastavení jazyka změňte preferovaný jazyk.

Pokud chcete přidat více jazyků tak prostudujte kapitolu [Návody](HowTo.md) - Jak vytvořit lokalizaci pro další jazyk.

## Jak můžu upravit vzhled aplikace?

Vzhled aplikace lze upravit modifikací HTML šablon a statického obsahu (css, js ...). Šablony může spravovat kdokoli, kdo rozumí HTML, není nutná žádná znalost Pythonu. Více informací o HTML šablonách naleznete v [Django dokumentaci](https://docs.djangoproject.com/en/5.0/ref/templates/).

V SVJIS projektu jsou HTML šablony uloženy v adresáři `/svjis/articles/templates` a ostatní statický obsah v adresáři `/svjis/articles/static`.

## Kde můžu aplikaci hostovat?

Možností je více, buď můžete aplikaci nainstalovat na Váš počítač a provozovat ho na serveru "pod stolem" - SVJIS běží docela dobře i na Raspberry Pi. Další možností je pronájem virtuálního privátního severu (VPS) u některé ze společností, které službu nabízejí. Já mám dobrou zkušenost s [Web4U](https://www.web4u.cz/cs/serverhosting/virtualni-serverhosting) varianta linux mini (1 virtuální CPU jádro, 1GB RAM, 20GB Disk).

Pokud máte někdo jinou dobrou zkušenost jak systém provozovat, tak nám dejte vědět.

## Nastavil jsem název společenství ale název se mi v záhlaví láme tam kde nechci.

V místě, kde se název láme a neměl by zadejte místo obyčejné mezery takzvanou nezlomitelnou mezeru.

* ve Windows podržte Alt a na numerické klávesnici napište 0160
* v Linuxu Ctrl+Shift+U a napište 00A0 a potvrďte Enterem

## Jak můžu vyrobit pro můj web ikonu (favicon.ico)

1. Vytvořte ve vešem oblíbeném editoru obrázků (např. Gimp) obrázek o rozměrech 192x192.
1. Eportujte obrázek jako `favicon.png`
1. Přejmenujte obrázek z `favicon.png` na `favicon.ico`
1. Nakopírujte `favicon.ico` do `/opt/mysvj_cz/www/`
