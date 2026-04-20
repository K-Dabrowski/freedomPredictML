# Wykorzystanie sztucznej inteligencji do analizy naruszeń praw człowieka na podstawie wskaźników społeczno-ekonomicznych
 
> **Praca inżynierska** &nbsp; | &nbsp; Uczenie maszynowe × Prawa człowieka
 
---
 
## O projekcie
 
Celem projektu jest zaprojektowanie, implementacja i ocena modelu uczenia maszynowego do **prognozowania poziomu przestrzegania praw człowieka** w 193 państwach członkowskich ONZ w horyzoncie czasowym **2025–2100**.
 
Zmienna objaśniana — *wskaźnik wolności* — pochodzi ze zbioru [Freedom in the World](https://freedomhouse.org/report/freedom-world). Składa się z dwóch komponentów: praw politycznych (PR) i wolności obywatelskich (CL), ocenianych pierwotnie w skali 1–7. W pracy dokonano inwersji skali (wartość 7 = maksymalna wolność) i ujednolicono oba komponenty do jednego wskaźnika jako ich średnią arytmetyczną.
 
Prognozy są generowane w trzech scenariuszach Wspólnych Ścieżek Społeczno-Ekonomicznych (**Shared Socioeconomic Pathways**):
 
| Scenariusz | Opis |
|---|---|
| **SSP1** | Zrównoważony rozwój — wzrost edukacji, spadek nierówności, silna współpraca międzynarodowa |
| **SSP2** | Kontynuacja trendów — umiarkowane, historyczne tempo postępu |
| **SSP3** | Rywalizacja regionalna — globalna fragmentacja, priorytetyzacja interesów narodowych |
 
---
 
## Struktura repozytorium
 
```
freedomPredictML/
│
├── notebooks_code_only/                       # Czysty kod
│   ├── 01_data_preparation_code.ipynb
│   └── 02_eda_modeling_code.ipynb
│
├── notebooks_full/                            # Kod + analiza
│   ├── 01_data_preparation_full.ipynb
│   └── 02_eda_modeling_full.ipynb
│
├── README.md
└── thesis.pdf                                 # Pełna treść pracy inżynierskiej
```
 
---
 
## Metodologia
 
### Notebook 01 — Przygotowanie danych
 
**Dane**
- 193 kraje ONZ, lata 1993–2024 (historyczne) oraz 2025–2100 (projekcje)
- 9 zmiennych objaśniających:

| Zmienna | Źródło | Opis |
|---|---|---|
| `p_density` | ONZ | Gęstość zaludnienia |
| `old_age` | ONZ | Wskaźnik obciążenia demograficznego osobami 65+ |
| `imr_value` | ONZ | Śmiertelność niemowląt (na 1000 żywych urodzeń) |
| `fertility_rate` | ONZ | Wskaźnik dzietności ogólnej |
| `net_migration` | ONZ | Całkowita migracja netto |
| `sex_ratio` | ONZ | Współczynnik maskulinizacji (mężczyźni na 100 kobiet) |
| `gdp_pc` | Gapminder / IIASA | PKB per capita PPP (USD, rok bazowy 2021) |
| `mean_school` | Wittgenstein Centre | Średnia liczba lat edukacji osób w wieku 25+ |
| `avg_temp` | World Bank Climate Portal | Średnia roczna temperatura krajowa |
 
**Imputacje**
- *Wskaźnik wolności* — braki geopolityczne rekonstruowano indywidualnie na podstawie historycznych odpowiedników państw oraz danych archiwalnych Freedom House
- *PKB per capita (SSP1/SSP3)* — współczynniki proporcji z modelu IIASA, dla 13 krajów bez danych IIASA zastosowano metodę proxy z doborem krajów referencyjnych
- *Lata edukacji (21 krajów)* — dane ONZ/UNDP → regionalny współczynnik proporcji oraz interpolacja **PCHIP**
- *Temperatura* — projekcje jako uśrednienie trzech modeli klimatycznych: MPI-ESM1-2-HR, MRI-ESM2-0, GFDL-ESM4
---
 
### Notebook 02 — EDA, modelowanie i prognozy
 
**EDA**
- Globalny trend wskaźnika wolności 1993–2024, mapa geograficzna, rozkłady i korelacje zmiennych

**Inżynieria cech**
- Transformacje logarytmiczne zmiennych w celu redukcji asymetrii rozkładu
- Agregacja krajów do 11 makroregionów geopolitycznych (standard MESSAGE)
- Profilowanie krajów — **PCA** na danych historycznych
- Standaryzacja zmiennych, selekcja cech przez **VIF**

**Modelowanie**
- Porównanie - Linear Regression, Ridge Regression, Random Forest, MLP, MLP Deep, **XGBoost**
- Walidacja `TimeSeriesSplit`, optymalizacja hiperparametrów **Optuna**
- Model finalny - XGBoost (R² = 0.88, MAE = 0.39)
- Analiza ważności cech **SHAP**, najsilniejszy predyktor - historyczny profil państwa

**Prognozy**
- Wskaźnik wolności dla 193 krajów w latach 2025–2100, 3 scenariusze SSP
- Trajektorie zmian statusu (Wolny / Częściowo Wolny / Niewolny), mapy porównawcze 2024 vs. 2100

---
 
## Technologie
 
```
Python 3.12
pandas · numpy · scipy · scikit-learn
xgboost · optuna · shap
matplotlib · seaborn · geopandas
country_converter · statsmodels
```
 
