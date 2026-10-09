# Opportunity Dataset — Data Cleaning, Analysis & Interactive Dashboard

**Program:** Excelerate — Data Visualization Trainee Program (Cohort 0308 SLU DVT)
**Role:** Documentation, Analysis Review & Project Coordination (Team 1)
**Team:** Abiodun Michael Ogunleye, Queen Bella, Aman Gupta, Fatima Saifuddin
**Tools:** Python (pandas), Microsoft Excel, Power BI Desktop

---

## Overview

This project took a raw, messy 5,733-record "Opportunity" dataset — internships, courses, competitions, and events listed on a learner platform — through a complete data pipeline: cleaning, exploratory analysis, and an interactive Power BI dashboard. The goal was to turn an unreliable export into a trustworthy, analysis-ready dataset and surface actionable insights for the platform's data owner.

**The core challenge:** over 70% of the raw file turned out to be internal test and demo data, not real opportunities — identifying and handling that correctly shaped every decision in this project.

---

## The Problem

The raw export had five distinct quality issues:

| Issue | Example |
|---|---|
| **Corrupted records** | JSON fragments embedded in normal fields (e.g. `%22label%22: %22Yemen%22`) |
| **Inconsistent formatting** | "Virtual" / "virtual" / "vitrual" all meaning the same thing |
| **Test/demo data** | 4,186 records with names like `Course Automation 1740727622332` |
| **Missing values** | 20+ fields with significant gaps |
| **Raw timestamp fields** | Dates stored as epoch-millisecond numbers |

## My Approach

I used a structured five-part quality framework — **M.I.I.D.E.** (Missing values, Incorrect entries, Inconsistent formatting, Duplicate/invalid records, Empty fields) — to make sure every issue category was identified and addressed systematically, not fixed ad hoc.

**The key decision: flag, don't delete.**
Rather than silently removing the 4,186 suspected test records, I flagged them in a new `is_test_or_placeholder` column and kept them in the dataset. This meant:
- Nothing was permanently lost or unrecoverable
- The decision stayed reversible and auditable by anyone reviewing the work
- Downstream analysis (Week 2/3) could filter them out for accuracy, while the full 5,728-record file remained intact as the single source of truth

This is the same reasoning a data team would apply in production: deletion is a one-way door, flagging isn't.

```python
# Simplified example of the flagging logic
mask_test_name = df['name'].str.contains(
    r'automation|\btest\b|\btesting\b|\bdemo\b', case=False, na=False
)
df['is_test_or_placeholder'] = mask_test_name.map({True: 'Yes - likely test/demo record'})
```

Result: **5,733 raw records → 5,728 after removing 5 corrupted rows → 1,542 genuine, analysis-ready opportunities.**

---

## Exploratory Data Analysis — Key Findings

**1. Sign-ups arrive in waves, not steady growth**
The busiest quarter (2025 Q4, 263 opportunities) had over 100× the volume of the quietest (2022 Q4, just 2). This points to opportunities being published in batches — likely tied to recruitment cycles or partner onboarding — rather than a continuous pipeline.

**2. One category dominates the platform**
Internship accounts for 52.1% of all opportunities — more than 5× the next largest category (Competition). Platform-wide metrics move almost entirely with whatever is happening inside this one category.

**3. Remote formats lead delivery**
Work From Home (644) and Virtual (509) together make up 75% of all opportunities with a recorded location. A quarter of records (387) had no location specified — flagged as a genuine data-completeness gap rather than hidden.

**4. Volume ≠ value (the most interesting finding)**
Despite Internship having 5× the volume of Course, **Course offers the highest median scholarship value (120) — nearly 3× higher than Internship's (42)**. The category with the most opportunities is not the category offering the most financial value.

**5. Honest data gaps, disclosed not hidden**
Two limitations were identified and reported rather than glossed over:
- *Outreach channel performance* cannot be measured — no field records how a participant discovered an opportunity
- *Scholarship currency* is unconfirmed — median values differ noticeably by currency (USD 120, EUR 67, INR 49) when cross-referenced against the fee field, suggesting values may need standardization before financial comparison

---

## Interactive Dashboard

Built in Power BI Desktop with four live filters (Category, Quarter, Location, Scholarship) and six core visualizations: sign-up trend, category volume, location distribution, scholarship availability, median scholarship by category, and a category × scholarship breakdown.

🔗 **Live Dashboard:** [https://drive.google.com/file/d/1iAT86DhpBbor2nXVcbFI31eiDe6zN0tH/view?usp=drive_link]

![Dashboard Screenshot](dashboard/dashboard_screenshot.png)

---

## Deliverables in This Repository

| File | Description |
|---|---|
| `data/Team1_Cleaned_Opportunity_Dataset.xlsx` | Final cleaned dataset with cleaning log, flag columns, and an isolated "Active Opportunities" sheet |
| `documentation/Data_Cleaning_Documentation.pdf` | Full Week 1 cleaning methodology and audit trail |
| `documentation/Week2_EDA_Report.pdf` | Exploratory data analysis with charts, tables, and findings |
| `documentation/Insights_Interpretation_Report.pdf` | Dashboard-companion report with recommendations |
| `presentation/Team1_Final_Presentation.pptx` | Final stakeholder presentation deck |

---

## What I'd Do Differently

Reviewer feedback on the Week 2 submission flagged that scholarship values weren't standardized to a single currency before comparison — a fair catch. In hindsight, currency standardization should have happened at the Week 1 cleaning stage, before any median or average was calculated, rather than being identified and documented as a limitation after the fact. This is noted as an open recommendation in the final report and would be the first fix in a second iteration of this project.

---

## About Me

I'm Abiodun Michael Ogunleye, founder of INxEX Tech Ltd. and currently building toward Big Four-style data consulting work. This project was part of my continued push into SQL and Python as core technical skills, alongside Excel and Power BI. More of my data work is available on this GitHub profile and on Medium.

**Connect:** [LinkedIn] · [Medium] · [Portfolio Site]
