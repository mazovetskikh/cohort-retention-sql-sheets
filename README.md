# Cohort Retention Analysis | SQL + Google Sheets
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=flat&logo=google-sheets&logoColor=white)](https://sheets.google.com/)

User retention analysis comparing organic vs promo acquisition quality across 6 monthly cohorts, built using PostgreSQL for data preparation and Google Sheets for cohort visualisation.

---

## Business Context

A product team wants to understand how well they retain users acquired through different channels — organic vs promotional campaigns. The analysis tracks user activity over a 6-month observation window (January–June 2025) and calculates monthly retention rates per cohort.

**Key questions:**
- How does retention differ between promo and organic users?
- Which cohorts retain best over time?
- Where do the biggest drop-offs occur?

---

## Project Structure

| File / Sheet | Description |
|---|---|
| `cohort_analysis.sql` | SQL query: date cleaning, cohort building, aggregation |
| Google Sheets — Data | Raw SQL output imported for analysis |
| Google Sheets — Cohort Tables | Interactive pivot tables with retention rates and slicer |
| Google Sheets — Conclusions | Written analysis and interpretation |

---

## Data Challenges

Both source tables (`cohort_users_raw` and `cohort_events_raw`) stored dates as inconsistent text strings — mixed delimiters (`.` `/` `-`), 2- and 4-digit years, and varying day/month formats. The SQL query handles full date standardisation before any analysis:

- Strips whitespace and time components
- Replaces all delimiters with `-` using `regexp_replace`
- Splits into day, month, year parts via `split_part`
- Reconstructs clean dates with `to_date`, converting 2-digit years to 4-digit using `CASE`

Additional filters remove: test events, NULL dates, NULL event types, and activity outside the observation window.

---

## SQL Approach

4-CTE pipeline:

```
data_parted_u      → parse and split user signup dates
date_parted_e      → parse and split event dates  
proper_date_u_e    → reconstruct clean dates, join users + events
cohort_table       → assign cohort_month and month_offset, apply filters
```

Final output: unique active users per `promo_signup_flag` + `cohort_month` + `month_offset`.

---

## Cohort Tables (All Users Combined)

<img width="557" height="290" alt="Cohort_Retention_Analysis" src="https://github.com/user-attachments/assets/b4f8e403-6ffc-4dd4-92ca-1d6b82ecf1c3" />

<a href="https://docs.google.com/spreadsheets/d/1WRT2tJ6sffCDvaqePhjH6VpLKvHrk1BRHP0ceX8XwP0/edit?gid=359253252#gid=359253252"><img src="https://img.shields.io/badge/OPEN%20IN%20GOOGLE%20SHEETS-34A853?style=for-the-badge&logo=googlesheets&logoColor=white"></a>

---

## Organic vs Promo: Key Numbers

| Metric | Organic | Promo |
|---|---|---|
| Jan cohort — Month 1 retention | 83% (58/70) | 62% (21/34) |
| Jan cohort — Month 3 retention | 74% (52/70) | 9% (3/34) |
| Jan cohort — Month 5 retention | **56%** (39/70) | **9%** (3/34) |
| Month 1 retention range (all cohorts) | 73–87% | 46–62% |

---

## Conclusions

The cohort table shows a stable — I'd even say high — retention rate overall. That said, organic users clearly retain better. Between 73–87% stay with the product into the next month, and even after six months, 56% are still active.

Promo users tell a different story. Not only are cohort sizes smaller from the start, but retention drops off much faster — by month 3 we've already lost more than half, and by month 6 only 9% remain, compared to 56% for organic. This suggests organic users have a genuine interest in the product and are more likely to stick around long-term.

**Promo users in detail:** Despite relatively stable retention in the first three months, there's a sharp drop from 50% to 9% between months 3 and 4 in the January cohort. This is worth investigating further — such an abrupt decline could indicate a sudden change in the product experience or a mismatch between what promo campaigns promise and what users actually find.

**Organic users in detail:** The largest cohort — January — retains at roughly the same rate as smaller cohorts in later months, which suggests cohort size doesn't significantly affect retention quality. February shows a slight dip, but the overall picture is stable. The uptick in March could be linked to a product event, promotional campaign, or seasonal offer.

Overall, the data points to a consistent user experience with periodic improvements. Organic acquisition drives predictable, stable retention — and shows a gradual upward trend.

---

## Tools & Techniques

**SQL (PostgreSQL / DBeaver)** 
- Multi-CTE pipeline for date cleaning and cohort construction
- `regexp_replace`, `split_part`, `trim` for text standardisation
- `to_date`, `date_trunc`, `EXTRACT`, `AGE` for date calculations
- `CASE` for conditional logic (2-digit year handling)
- Filtering of test events and out-of-window activity

**Google Sheets**
- Pivot tables for cohort structure
- Retention rate formulas with relative referencing
- Conditional formatting (gradient heatmap)
- Interactive slicer filtering by `promo_signup_flag`

<a href="https://github.com/mazovetskikh/cohort-retention-sql-sheets/blob/main/cohort_analysis.sql"><img src="https://img.shields.io/badge/VIEW%20SQL%20SCRIPT-336791?style=for-the-badge&logo=postgresql&logoColor=white"></a><a href="https://docs.google.com/spreadsheets/d/1WRT2tJ6sffCDvaqePhjH6VpLKvHrk1BRHP0ceX8XwP0/edit?gid=359253252#gid=359253252"><img src="https://img.shields.io/badge/VIEW%20COHORT%20TABLES%20IN%20GOOGLE%20SHEETS-34A853?style=for-the-badge&logo=googlesheets&logoColor=white"></a>
