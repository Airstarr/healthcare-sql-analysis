# Healthcare Database Analysis — PostgreSQL

## Project Overview
This project involves querying a relational healthcare database using PostgreSQL to extract clinical and operational insights. The analysis covers patient demographics, appointment patterns, medication management, billing behaviour, and doctor performance across a multi-table schema.

---

## Database Schema

**Database:** `healthcare`
**Tool:** pgAdmin 4 · PostgreSQL 18
**Tables:** 6

| Table | Description |
|---|---|
| `patients` | Patient demographics including name, city, date of birth, and insurance provider |
| `appointments` | Appointment records including date, status, and attending doctor |
| `doctors` | Doctor details including name and clinic location |
| `diagnoses` | Diagnosis records linked to appointments via diagnosis codes |
| `medications` | Medication records linked to patients including dosage and end date |
| `billing` | Billing records per appointment including amount charged and insurance covered |

---

## Queries & Business Questions Answered

### Patient Analysis
- **Patients by location** — Retrieved all patients residing in Seattle using a city filter
- **Patients with multiple diagnoses** — Identified patients who received more than one diagnosis in 2024 using `HAVING COUNT > 1` on grouped diagnosis records
- **Patients with low insurance coverage** — Flagged patients whose insurance covered less than 70% of their total bill by calculating the insurance-to-amount ratio per patient
- **Patients above average billing** — Listed patients whose total billed amount exceeded the database-wide average billing amount using a subquery in the `HAVING` clause

### Appointment Analysis
- **Completed appointments by month** — Filtered all completed appointments in February 2024 using `EXTRACT(month)` and `EXTRACT(year)` on the appointment date (note: no records existed for that period)
- **Peak days of the week** — Determined which days of the week had the highest appointment volume using `to_char(appointment_date, 'day')` grouped by day
- **Most recent appointment per patient** — Retrieved each patient's latest appointment date using `MAX(appointment_date)` with a patient-level group by

### Doctor Performance
- **Appointments completed per doctor** — Counted distinct appointments per doctor using a JOIN between the doctors and appointments tables
- **Doctor revenue ranking** — Ranked doctors by total billing revenue generated using the `RANK()` window function ordered by `SUM(billing amount)` descending
- **Lowest no-show rate** — Identified the doctor with the fewest no-show appointments as a percentage of total appointments using two CTEs — one for no-show counts and one for total appointment counts

### Diagnosis & Medication Analysis
- **Most common diagnosis** — Found the most frequently occurring diagnosis code across all records using `COUNT` grouped by `diagnosis_code`, ordered descending with `LIMIT 1`
- **Diabetic patients and last medication date** — Used a CTE to first isolate patients with diagnosis code `E11` (Type 2 Diabetes), then joined to the medications table to retrieve each patient's most recent medication end date
- **Age group with highest hypertension incidence** — Used two CTEs to first calculate patient ages from date of birth, then bucket patients into age groups (0–17, 18–34, 35–49, 50–64, 65+) using a `CASE` statement, then filtered for diagnosis code `I10` (Hypertension) to find the most affected age group
- **Medications per patient** — Counted the number of distinct medications prescribed per patient using both a direct `GROUP BY` method and a CTE-based approach, then aggregated to show how many patients share the same medication count

### Insurance & Billing Analysis
- **Insurance provider with highest average coverage** — Identified which insurance provider covers the highest average billing amount using `ROUND(AVG(insurance), 2)` grouped by provider
- **Top diagnosis per clinic location** — Used a CTE with `RANK() OVER (PARTITION BY clinic_location)` to find the most common diagnosis at each clinic location, then filtered for `rank = 1`

---

## SQL Concepts Demonstrated

| Concept | Used In |
|---|---|
| `WHERE` filters | Patient city filter, medication dosage filter, appointment status |
| `JOIN` (multiple tables) | Doctor revenue, diabetic patients, billing coverage |
| `GROUP BY` + `HAVING` | Multi-diagnosis patients, above-average billing |
| `EXTRACT()` | Appointment month/year filtering, patient age calculation |
| `CASE` statements | Age group bucketing |
| Window functions (`RANK() OVER`) | Doctor revenue ranking, top diagnosis per clinic |
| CTEs (`WITH ... AS`) | Diabetic patients, no-show rate, age group analysis, top clinic diagnosis |
| Subqueries | Average billing comparison |
| `CAST` / `REPLACE` | Stripping units from dosage field for numeric comparison |
| Aggregate functions | `COUNT`, `SUM`, `AVG`, `MAX`, `ROUND` |

---

## How to Run

```sql
-- Connect to the healthcare database in pgAdmin or psql
-- Open healthcare_queries.sql
-- Run queries individually or all at once
```

**Requirements:** PostgreSQL 18 · pgAdmin 4

---

## Files in this Repository

| File | Description |
|---|---|
| `healthcare_queries.sql` | All SQL queries with comments |
| `README.md` | This file |

---

*Analysis by [Ojo Esther](https://www.linkedin.com/in/estherojo-data)*
