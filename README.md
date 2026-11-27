# HR-claude-automation

A Power BI project (PBIP format) for HR analytics: an overview dashboard on employee attrition, and an HR-only page that shows who might leave next and what HR can do about it.

The project is stored as text (TMDL for the model, PBIR for the report), so it works with Git and can be edited outside Power BI Desktop.

## Contents

| Path | What it is |
|---|---|
| `HR-claude-automation.pbip` | Open this in Power BI Desktop |
| `HR-claude-automation.SemanticModel/` | Data model: HR table, measures, `DataFolder` parameter |
| `HR-claude-automation.Report/` | Report definition: pages and visuals |
| `Data/HR_Analytics.csv` | Source dataset (1,480 rows, 37 columns) |

## Getting started

1. Install Power BI Desktop with **Power BI Project (.pbip)** and **TMDL** preview features enabled (File > Options > Preview features).
2. Open `HR-claude-automation.pbip`.
3. Set the data path. The `DataFolder` parameter in `HR-claude-automation.SemanticModel/definition/expressions.tmdl` is an absolute path on the original machine. Change it to the full path of this repo's `Data\` folder (with a trailing backslash), or edit it in Power Query under **Transform data > Manage parameters**.
4. Select **Refresh**.

## Data

Source: `Data/HR_Analytics.csv`, loaded into one table called `HR`. Power Query steps:

- `TravelRarely` is changed to `Travel_Rarely`, and `Travel-Frequently` to `Travel_Frequently`, in BusinessTravel.
- Blank YearsWithCurrManager values are set to 0.
- Duplicate rows are removed on EmpID (1,480 rows become 1,464 employees).
- Numeric columns are typed as numbers. AgeGroup, SalarySlab and Department are text.
- Hidden sort columns keep AgeGroup (18-25 to 55+) and SalarySlab (0-3 LPA to 10+ LPA) in logical order.

Calculated columns on `HR` (used by the HR (OWN) page):

- **Risk Score** (0 to 7): one point each for overtime, frequent travel, job satisfaction ≤ 2, work-life balance ≤ 2, environment satisfaction ≤ 2, tenure ≤ 1 year, and no stock options.
- **Tenure Band** and **Distance Band**, each with a sort column.

## Measures

All measures live in the `_Measures` table.

| Page | Measures |
|---|---|
| HR Analytics | Total Employees, Active Employees, Attrition Count, Attrition Rate %, Average Age, Avg Experience |
| HR (OWN) | Active At Risk, High Risk Watchlist Count, Overtime %, First Year Active, No Promotion 5Y Active, Avg Job Satisfaction, Avg Work Life Balance, Avg Environment Satisfaction, Avg Relationship Satisfaction, Avg Salary Hike % |

## Report pages

### 1. HR Analytics
Overview for everyone. Age Group and Department slicers filter the whole page.

- Six KPI cards: employees, active, attrition count, attrition rate, average age, average experience
- Attrition by Department (donut)
- Attrition by Salary Slab (clustered bar)
- Attrition by Job Role and Job Satisfaction (matrix with gradient)
- Age Group Distribution, Attrition by Gender, Attrition Trend by Experience, Department-wise Employee Count

### 2. HR (OWN)
Retention risk, for the HR team only. A Department dropdown filters the whole page.

- Six KPI cards: Active At Risk, High-Risk Watchlist, Overtime %, First Year Active, No Promotion 5Y+, Avg Job Satisfaction
- High-Risk Watchlist table: active employees with Risk Score of 4 or more
- Attrition Rate % by Risk Score, Tenure Band, Overtime, Business Travel, Stock Option Level and Commute Distance
- Satisfaction Heatmap (matrix of average satisfaction scores by department)

## Headline numbers (all departments)

| Measure | Value |
|---|---|
| Total employees | 1,464 |
| Active / Attrition | 1,229 / 235 |
| Attrition rate | 16.1% |
| Active At Risk | 194 |
| High-risk watchlist | 94 |
| Overtime % | 28.3% |

Attrition rises with Risk Score: 2.8% at score 0, 21.2% at 3, 45.8% at 4 and 67.9% at 5.

## Notes

- Power BI Desktop does not reload a project when files change on disk. Close it without saving and reopen the `.pbip` after pulling or editing files outside Desktop.
- The dataset contains employee-level records. Check that sharing the repo is appropriate before making it public.
