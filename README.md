# Finanční report Česko a Evropa (2006–2018)

Power BI report, který ukazuje, jak se v letech **2006–2018** vyvíjela kupní síla v Česku a jak si ČR stojí ve srovnání se zbytkem Evropy.

Repozitář obsahuje:

- `financni-report-cesko-evropa.pbix` – kompletní Power BI report (včetně datového modelu)

---

## 1. Datový základ

Použit byl dataset z Datové akademie (Engeto):

- **Česko – potraviny & mzdy**
  - ceny vybraných potravin (2006–2018)
  - průměrná mzda
  - počet jednotek zboží, které lze koupit z jedné průměrné mzdy

- **Evropa – makroekonomická data**
  - **HDP** (v běžných cenách)
  - **populace**
  - **Gini index** (nerovnost příjmů)
  - meziroční růst HDP v %

### Transformace dat (Power Query)

V Power Query proběhlo:

- filtrování na období **2006–2018** (starší roky odstraněny),
- odstranění duplicitních řádků v evropské tabulce,
- přepočet a vytvoření nových sloupců:
  - `GDP (mld)` – HDP převedené na miliardy,
  - `Population (mil)` – populace v milionech.

Datový model v PBIX je tedy **samostatný**, pro otevření reportu **není potřeba zdrojový Excel**.

---

## 2. Datový model v Power BI

Hlavní tabulky:

- `primary` – Česko, potraviny, ceny, mzdy, YoY inflace potravin.
- `gdp_by_year` – meziroční změny HDP, mezd a inflace potravin v ČR.
- `secondary` – evropské země (HDP, populace, Gini, růst HDP).
- `slowest_price_increase` – pomocná tabulka pro nejpomaleji zdražující položky (využitá v prvním projektu, zde spíše doplňkově).

Vztahy:

- `primary[year]` ↔ `gdp_by_year[year]`
- `secondary` je samostatná tabulka pro evropskou stránku (filtrování přes `country` a `year`).

---

## 3. Struktura reportu

Report má **4 stránky**:

### 1) `Úvod`

Nástěnka s navigací a kontextem:

- název: **„Finanční report Česko a Evropa (2006–2018)”**
- krátký text vysvětlující, co report ukazuje,
- tři velká tlačítka (bookmarky) přepínající na jednotlivé stránky:
  - **Česko – přehled**
  - **Nákupní košík**
  - **Evropa – země**

### 2) `Česko – přehled`

Základní přehled pro ČR.

Prvky:

- **slicer roků** (2006–2018),
- tři karty s průměrnou meziroční změnou:
  - *Inflace potravin* (%),
  - *Růst mezd* (%),
  - *Růst HDP* (%),
- kombinovaný line chart:
  - osa X: rok,
  - osa Y: meziroční změna v %,
  - série: inflace potravin, růst mezd, růst HDP,
- stacked column chart:
  - ukazuje, **kolik jednotek vybraných potravin** lze koupit z průměrné mzdy (Top 5 kategorií).

### 3) `Nákupní košík`

Detailní pohled na jednotlivé potraviny.

Prvky:

- slicer **„Produkt“** (kategorie potravin),
- slicer roků (2006–2018),
- tabulka:
  - průměrná cena za rok,
  - počet položek v košíku (kolik jednotek lze koupit z průměrné mzdy),
- horizontální bar chart:
  - X: **meziroční rozdíl cen (%)**,
  - Y: produkt,
  - umožňuje rychle vidět, které položky zdražovaly nejvíc / nejmíň.

### 4) `Evropa – země`

Srovnání evropských zemí.

Prvky:

- slicer **„Země“** – multi-select,
- slicer **rok** (tlačítková vizualizace),
- mapový vizuál:
  - bubliny podle HDP a polohy,
- scatter plot:
  - X: průměrný meziroční růst HDP (%),
  - Y: průměrný Gini,
  - velikost bubliny: populace (mil),
- tabulka:
  - země,
  - HDP (mld),
  - populace (mil),
  - průměrný meziroční růst HDP (%),
  - průměrný Gini.

Vše filtrováno podle vybraného roku a zemí.

---

## 4. Míry (measures) a výpočty

Vybrané DAX míry (ukázkově):

- `Avg Food YoY % = AVERAGE(gdp_by_year[food_yoy_pct]) / 100`
- `Avg Wage YoY % = AVERAGE(gdp_by_year[wage_yoy_pct]) / 100`
- `Avg GDP YoY % = AVERAGE(gdp_by_year[gdp_yoy_pct]) / 100`

> Poznámka: původní hodnoty jsou v procentech jako celé číslo (např. 3,18), proto dělení 100 a následné formátování jako `%`.

Další výpočty:

- medián / průměr `price_yoy_pct` pro porovnání produktů v Nákupním košíku,
- přepočet HDP a populace na „čistší“ jednotky (miliony/miliardy) jako **počítané sloupce** v Power Query / modelu.

Tím je splněn požadavek zadání:  
> „Vytvoření alespoň 1 measure (metrika/míra) a 1 kalkulovaného sloupce/tabulky.“

---

## 5. Jak report spustit

1. Naklonuj nebo stáhni repo:
   ```bash
   git clone https://github.com/<tvuj-ucet>/<nazev-repa>.git
