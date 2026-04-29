# FDUS Portfolio Analysis — Sample Deliverable

## Summary

Working sample extracting Fidus Investment Corporation's (FDUS, CIK 0001513363) FY2025 10-K filing (filed 2026-02-26, accession 0001193125-26-076572, 16.9MB raw HTML) into structured portfolio data. Parses the full Schedule of Investments (241 investment rows, 103 portfolio companies, $1,324,753K total fair value), ranks companies by aggregate fair value, and performs a 1-company deep dive on InductiveHealth Informatics — extracting all filing data, scraping the company website, and identifying top executives. Built as a scope-alignment exercise before a full 5-company paid pilot.

## Top 10 Portfolio Companies by Total Fair Value

| Rank | Company | FV ($K) | # Inv | Investment Types |
|------|---------|--------:|------:|-----------------|
| 1 | Pfanstiehl, Inc. | 40,995 | 1 | Common Equity |
| 2 | InductiveHealth Informatics, LLC | 38,389 | 4 | Common Equity; First Lien Debt; Preferred Equity |
| 3 | Fishbowl Solutions, LLC | 35,063 | 3 | First Lien Debt; Revolving Loan |
| 4 | American AllWaste LLC (dba WasteWater Transport Services) | 33,944 | 10 | Common Equity; First Lien Debt; Preferred Equity |
| 5 | GMP HVAC, LLC (dba McGee Heating & Air, LLC) | 31,350 | 2 | First Lien Debt; Preferred Equity |
| 6 | Spectra A&D Acquisition, Inc. | 31,197 | 6 | Common Equity; First Lien Debt |
| 7 | Detechtion Holdings, LLC | 31,061 | 4 | Common Equity; First Lien Debt; Revolving Loan; Subordinated Debt |
| 8 | Barefoot Mosquito and Pest Control, LLC | 30,913 | 4 | Common Equity; First Lien Debt; Preferred Equity |
| 9 | ServicePower, Inc. | 30,038 | 2 | First Lien Debt |
| 10 | Dealerbuilt Acquisition, LLC | 27,310 | 4 | Common Equity; First Lien Debt; Preferred Equity; Subordinated Debt |

Top 10 = $330,260K (24.9% of portfolio). Source: `data/fdus_top10_by_fair_value.csv`

## Deep Dive: InductiveHealth Informatics (Rank #2, $38.4M FV)

### Filing Data

4 investment rows across 3 types (First Lien Debt, Preferred Equity, Common Equity). Non-control/Non-affiliate category. Initial investment 9/20/2024; $3.0M add-on tranche funded 12/16/2025, same terms. Aggregate cost $38,031K, fair value $38,389K — slight appreciation, investment performing at or above par. Debt rate: SOFR + 7.75% with 2.00% floor, yielding 11.71% cash + 0.50% PIK. Maturity 9/20/2028. No mentions in MD&A, risk factors, or footnotes (performing normally — BDCs only flag problem credits in narrative sections).

| # | Type | Rate | Date | Maturity | Principal ($K) | Cost ($K) | FV ($K) |
|---|------|------|------|----------|---------------:|----------:|--------:|
| 1 | First Lien Debt | S+7.75%/2.00% floor, 11.71%/0.50% PIK | 9/20/2024 | 9/20/2028 | 35,065 | 34,775 | 35,065 |
| 2 | First Lien Debt | S+7.75%/2.00% floor, 11.71%/0.50% PIK | 12/16/2025 | 9/20/2028 | 2,993 | 2,964 | 2,994 |
| 3 | Preferred Equity | — | 9/20/2024 | — | — | 292 | 330 |
| 4 | Common Equity | — | 9/20/2024 | — | — | — | — |

### Company Overview (from inductivehealth.com)

Public health surveillance and immunization management software for US state and local health departments. Five product platforms: InductiveHealth EDSS (cloud-native disease surveillance), EpiTrax (open-source configurable EDSS), NBS (CDC's National Electronic Disease Surveillance System — InductiveHealth is the largest implementation partner), WebIZ (immunization information system for vaccine operations, registries, and school compliance), and ESSENCE (syndromic surveillance, built with Johns Hopkins Applied Physics Laboratory). WordPress site, all pages server-rendered static HTML.

### Top 3 Executives

| Rank | Name | Title | Source |
|------|------|-------|--------|
| 1 | Eric Whitworth | Chief Executive Officer | inductivehealth.com/about-us/ |
| 2 | Gary Lawrence | Chief Financial Officer | inductivehealth.com/about-us/ |
| 3 | Greg Smith | Chief Information Security Officer | inductivehealth.com/about-us/ |

LinkedIn profile enrichment deferred pending ToS compliance discussion — recommend Apollo.io or Crunchbase API if scoped.

### Full Filing Data

- `data/inductivehealth_filing_data.json` — all 4 Schedule rows + summary + other filing mentions
- `data/inductivehealth_website.json` — 9 pages scraped (homepage, services, about, contact, 5 product pages)
- `data/inductivehealth_execs.csv` / `.json` — final top 3 executives with ranking methodology

## Data Outputs

- `data/fdus_schedule_full.csv` — full Schedule of Investments: 241 rows, 103 companies, 11 columns (company_name, industry, investment_category, investment_type, rate_spread_floor, rate_cash_pik, investment_date, maturity_date, principal_amount, cost, fair_value)
- `data/fdus_top10_by_fair_value.csv` — top 10 companies by aggregate fair value with investment type breakdown
- `data/inductivehealth_filing_data.json` — InductiveHealth: all Schedule rows, summary stats, cross-filing mention snippets
- `data/inductivehealth_website.json` — InductiveHealth: 9 scraped pages with clean text, leadership, product headings
- `data/inductivehealth_execs.csv` — top 3 executives (CSV format for spreadsheet import)
- `data/inductivehealth_execs.json` — top 3 executives (JSON format with metadata)

## Methodology

Filing retrieved via SEC EDGAR REST API (edgartools 5.30.0), not web scraping. Schedule of Investments parsed from raw HTML using BeautifulSoup — edgartools does not provide a typed parser for BDC investment schedules (its fund holdings support covers N-PORT filings, not BDC Schedules per the Investment Company Act). Website scraped with requests + BeautifulSoup (static WordPress, no headless browser required). Full technical detail in [methodology.md](methodology.md). Parser is reusable across other BDCs with minor field-mapping adjustments.

## Scaling to Full Scope

Two axes:

1. **Remaining 4 companies in FDUS top 5** — estimated 4 additional hours, mostly website scraping and executive identification. The filing parser and ranking logic are already done.
2. **Additional BDCs** (MAIN, ARCC, HTGC, GBDC, etc.) — estimated 2-3 hours parser tuning per BDC (column header variations, footnote conventions), then the same pipeline applies. The three-category structure (Control / Affiliate / Non-control) is universal per Investment Company Act Section 2(a)(3).

## Repository Structure

```
fdus-sample/
├── README.md                   # this file
├── methodology.md              # technical methodology
├── .gitignore                  # excludes venv/, raw/, *.html, .env
├── src/
│   ├── fetch_filing.py         # fetch + cache FDUS 10-K from EDGAR
│   ├── locate_schedule.py      # find Schedule of Investments tables in HTML
│   ├── parse_schedule.py       # parse 241 investment rows → CSV
│   ├── rank_top_companies.py   # aggregate by company, rank by fair value
│   ├── extract_company_filing_data.py  # deep-dive filing extraction
│   ├── scrape_company_website.py       # website scrape (InductiveHealth)
│   └── finalize_execs.py       # executive ranking + output
└── data/
    ├── fdus_schedule_full.csv
    ├── fdus_top10_by_fair_value.csv
    ├── inductivehealth_filing_data.json
    ├── inductivehealth_website.json
    ├── inductivehealth_execs.csv
    └── inductivehealth_execs.json

# raw/ not shipped — 16.9MB of cached EDGAR HTML, reproducible via: python src/fetch_filing.py
```
