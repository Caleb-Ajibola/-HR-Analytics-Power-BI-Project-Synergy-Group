# HR Analytics Dashboard – Power BI Portfolio Project

## Executive Summary

This comprehensive Power BI dashboard delivers actionable workforce insights for Synergy Group's leadership and HR team. The solution provides real-time monitoring of workforce dynamics, hiring trends, diversity metrics, employee performance, and attrition analysis to support strategic decision-making.

## Project Objectives

The dashboard enables stakeholders to:

- Monitor workforce size and organizational structure
- Understand hiring trends and recruitment patterns
- Track diversity and inclusion metrics
- Analyze attrition patterns and identify key drivers

## Dashboard Architecture

### Report Pages

- **Overview** – High-level workforce metrics and KPIs
- **Demographics** – Detailed diversity and demographic analysis

### Data Model Design

The solution implements a star schema architecture with the following components:

#### Dimension Tables

- **DimEmployee** – Employee master data
- **DimDate** – Calendar dimension for time intelligence
- **DimEducationLevel** – Education classification lookup
- **DimSatisfiedLevel** – Satisfaction rating scale
- **DimRatingLevel** – Performance rating scale

#### Fact Table

- **FactPerformanceRating** – Employee performance reviews and ratings

## Implementation Guide

### 1. Data Loading & Preparation

All datasets were imported as CSV files from a local directory:

| Source File | Target Table |
|------------|--------------|
| Employee.csv | DimEmployee |
| EducationLevel.csv | DimEducationLevel |
| PerformanceRating.csv | FactPerformanceRating |
| RatingLevel.csv | DimRatingLevel |
| SatisfiedLevel.csv | DimSatisfiedLevel |

### 2. Data Model Relationships

The star schema includes both active and inactive relationships to support flexible analysis:

```
DimDate[Date] ────(active)────► FactPerformanceRating[ReviewDate]
DimDate[Date] ───(inactive)───► DimEmployee[HireDate]

DimEducationLevel[EducationID] ──(active)──► DimEmployee[EducationID]

DimSatisfiedLevel[SatisfactionID] ──(many-to-one)──►
    ├── FactPerformanceRating[EnvironmentSatisfaction]   (active)
    ├── FactPerformanceRating[JobSatisfaction]           (inactive)
    ├── FactPerformanceRating[RelationshipSatisfaction]  (inactive)
    └── FactPerformanceRating[WorkLifeBalance]           (inactive)

DimRatingLevel[RatingID] ──(many-to-one)──►
    ├── FactPerformanceRating[SelfRating]      (active)
    └── FactPerformanceRating[ManagerRating]   (inactive)
```

> **Technical Note:** Multiple inactive relationships require extensive use of `USERELATIONSHIP()` in DAX measures to activate specific relationship paths during calculations.

## DAX Measures Library

### Core Headcount Metrics

```dax
TotalEmployees = 
    DISTINCTCOUNT(DimEmployee[EmployeeID])

ActiveEmployees = 
    CALCULATE(
        [TotalEmployees],
        DimEmployee[Attrition] = "No"
    )

InactiveEmployees = 
    CALCULATE(
        [TotalEmployees],
        DimEmployee[Attrition] = "Yes"
    )
```

### Time Intelligence for Cohort Analysis

These measures enable analysis of employee cohorts by hire date:

```dax
TotalEmployeesDate = 
    CALCULATE(
        [TotalEmployees],
        USERELATIONSHIP(DimEmployee[HireDate], DimDate[Date])
    )

InactiveEmployeesDate = 
    CALCULATE(
        [InactiveEmployees],
        USERELATIONSHIP(DimEmployee[HireDate], DimDate[Date])
    )

% Attrition Rate Date = 
    DIVIDE(
        [InactiveEmployeesDate],
        [TotalEmployeesDate],
        0
    )
```

### Satisfaction Metrics

Leveraging inactive relationships to measure multiple satisfaction dimensions:

```dax
EnvironmentSatisfaction = 
    CALCULATE(
        MAX(FactPerformanceRating[EnvironmentSatisfaction]),
        USERELATIONSHIP(
            FactPerformanceRating[EnvironmentSatisfaction],
            DimSatisfiedLevel[SatisfactionID]
        )
    )

JobSatisfaction = 
    CALCULATE(
        MAX(FactPerformanceRating[JobSatisfaction]),
        USERELATIONSHIP(
            FactPerformanceRating[JobSatisfaction],
            DimSatisfiedLevel[SatisfactionID]
        )
    )

RelationshipSatisfaction = 
    CALCULATE(
        MAX(FactPerformanceRating[RelationshipSatisfaction]),
        USERELATIONSHIP(
            FactPerformanceRating[RelationshipSatisfaction],
            DimSatisfiedLevel[SatisfactionID]
        )
    )

WorkLifeBalance = 
    CALCULATE(
        MAX(FactPerformanceRating[WorkLifeBalance]),
        USERELATIONSHIP(
            FactPerformanceRating[WorkLifeBalance],
            DimSatisfiedLevel[SatisfactionID]
        )
    )
```

### Additional Business Metrics

```dax
AverageSalary = 
    AVERAGE(DimEmployee[Salary])
    -- Format: Currency, 0 decimals
```

## Key Findings & Insights

### Overall Attrition Analysis

**Company-Wide Attrition Rate (2012–2022): 16.1%**

This 10-year benchmark provides context for evaluating departmental and demographic trends.

### Department-Level Performance

| Department | Attrition Rate | Risk Level |
|-----------|---------------|-----------|
| Sales | 40.0% | 🔴 Critical |
| Engineering | 2.7% | 🟢 Low |

**Recommendation:** Immediate investigation into Sales department retention strategies, compensation structure, and work environment factors.

### Business Travel Impact

Attrition rates correlate directly with travel frequency:

- **Frequent Travel:** 25.0% attrition
- **Limited Travel:** 15.0% attrition
- **No Travel:** 8.0% attrition

**Insight:** Travel requirements increase turnover risk by 3x, suggesting need for travel compensation policies or role redesign.

### Demographic Attrition Patterns

#### Age Distribution

| Age Group | Attrition Rate | Population Share |
|-----------|---------------|-----------------|
| 20–29 | 20.0% | ~50% |
| <20 | 14.8% | ~20% |
| 30–39 | 12.3% | ~20% |
| 40–49 | 6.8% | ~8% |
| 50+ | 9.5% | ~2% |

**Critical Finding:** Employees under 30 represent 70% of the workforce but experience 35.7% combined attrition rate.

#### Gender Analysis

- **Male Employees:** 17.5% attrition
- **Female Employees:** 15.4% attrition

Gender gap suggests slightly higher retention challenges for male employees.

## Strategic Recommendations

### High-Priority Actions

1. **Young Employee Retention Program**
   - Develop targeted initiatives for employees aged 20–29, including mentorship, career pathing, and competitive compensation reviews.

2. **Sales Department Intervention**
   - Conduct exit interviews, benchmark compensation against industry standards, and assess management practices.

3. **Travel Policy Review**
   - Implement travel allowances, rotation programs, or remote work alternatives for high-travel roles.

### Monitoring & Risk Management

**Employees Requiring Special Attention:**

- Age groups: Under 20 to 29 years old (combined 35.7% attrition risk)
- Departments: Sales team members
- Travel profiles: Frequent travelers
- Review status: Employees overdue for performance reviews

## Technical Specifications

- **Platform:** Microsoft Power BI Desktop
- **Data Sources:** CSV files (local storage)
- **Schema:** Star schema with multiple inactive relationships
- **DAX Complexity:** Extensive use of relationship manipulation and time intelligence
- **Refresh Capability:** Manual refresh from local files

## Conclusion

This HR Analytics solution transforms raw employee data into strategic insights, enabling Synergy Group to proactively address retention challenges, optimize workforce planning, and improve organizational health. The flexible data model supports both historical trend analysis and real-time monitoring of critical HR metrics.

---

## Repository Structure

```
├── Data/
│   ├── Employee.csv
│   ├── EducationLevel.csv
│   ├── PerformanceRating.csv
│   ├── RatingLevel.csv
│   └── SatisfiedLevel.csv
├── Dashboard/
│   └── HR_Analytics_Dashboard.pbix
├── Screenshots/
│   ├── overview_page.png
│   └── demographics_page.png
└── README.md
```

## Contact

For questions or collaboration opportunities, please reach out via [LinkedIn](#) or [email](#).

## License

This project is available under the MIT License.
