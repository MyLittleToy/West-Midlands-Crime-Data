# West-Midlands-Crime-Data
Exploration of WM crime data using SQL


# West Midlands Crime Analysis – SQL Project

SQL analysis of crime data across the West Midlands region, examining crime distribution, hotspots, outcomes, and temporal trends over six months.

## Project Overview
This project analyses crime data obtained from the UK Office for National Statistics (ONS), focusing on a six-month period from March to August 2023. The analysis explores crime patterns, geographical hotspots, outcome correlations, and temporal trends to provide actionable insights for law enforcement and community safety planning.

---

## Dataset

**Source:** Office for National Statistics (ONS) – [https://www.ons.gov.uk/](https://www.ons.gov.uk/)

**Time Period:** March – August 2023 (6 months)
**Geographic Focus:** West Midlands region
**Original Format:** Separate CSV files for each month
**Data Volume:** Due to file size constraints, the analysis focuses on the most recent six-month period

---

## Tools Used

| Tool | Purpose |
|------|---------|
| SQL Server | Database creation and query execution |
| T-SQL | Data manipulation and analysis |
| ONS Data Portal | Source data acquisition |

---

## Project Steps

### STEP 1: Database Creation

Created a dedicated database to store and analyse the crime data.

```sql
CREATE DATABASE West_Midland_Crime;

### STEP 2: Data Consolidation
Combined all six monthly tables into a single unified table using UNION ALL for comprehensive analysis.

```sql
USE West_Midlands_Crime;

SELECT * INTO CombinedCrimeData
FROM dbo.[2023-03-west-midlands-street]
UNION ALL
SELECT * FROM dbo.[2023-04-west-midlands-street]
UNION ALL
SELECT * FROM dbo.[2023-05-west-midlands-street]
UNION ALL
SELECT * FROM dbo.[2023-06-west-midlands-street]
UNION ALL
SELECT * FROM dbo.[2023-07-west-midlands-street]
UNION ALL
SELECT * FROM dbo.[2023-08-west-midlands-street];

### STEP 3: Analysis Questions
The analysis addressed six key business questions:
- Most popular locations where crime is committed across the region
- Most common types of crime
- Geographical hotspots for specific crimes
- Outcomes of reported crimes
- Correlation between crime type and outcome
- Analysis of how specific crimes trend over time

### Analysis Results
Question 1: Distribution of Crimes by Location
```sql
SELECT 
    location, 
    COUNT(*) AS crime_count
FROM 
    CombinedCrimeData
GROUP BY 
    location
ORDER BY 
    crime_count DESC;

```sql
SELECT 
    crime_type, 
    COUNT(*) AS crime_count
FROM 
    CombinedCrimeData
GROUP BY 
    crime_type
ORDER BY 
    crime_count DESC;

    Location                                           Crime_count
    -------------------------------------------------- -----------
    On or near unspecified                             9906
    On or near Parking Area                            6036
    On or near Supermarket                             5365
    On or near Shopping Area                           3324
    On or near Petrol Station                          3015
    On or near High Street                             981
    On or near Hospital                                947
    On or near Nightclub                               934
    On or near Sports/Recreation Area                  891
    On or near New Street                              631

Insight: The majority of crimes occur at "unspecified" locations, followed by parking areas and supermarkets. Commercial locations (supermarkets, shopping areas, petrol stations) feature prominently in the top ten.

---

Question 2: Most Common Types of Crime

--Most Common Type of Crime
    SELECT crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    GROUP BY crime_type
    ORDER BY crime_count DESC;

    Crime_type                                         Crime_count
    -------------------------------------------------- -----------
    Violence and sexual offences                       75867
    Vehicle crime                                      17701
    Criminal damage and arson                          14237
    Anti-social behaviour                              13497
    Public order                                       13448
    Other theft                                        12829
    Shoplifting                                        12051
    Burglary                                           9581
    Robbery                                            4412
    Drugs                                              3912
    Possession of weapons                              3522
    Other crime                                        3191
    Theft from the person                              2019
    Bicycle theft                                      1143
    
Insight: Violence and sexual offences dominate the crime landscape, accounting for more than the next four crime types combined. Vehicle crime is the second most common offence.

---

Question 3: Geographical Hotspots for Specific Crimes

    USE		West_Midlands_Crime
    
    SELECT top 10 location, crime_type, COUNT(*) AS crime_count 
    FROM CombinedCrimeData
    GROUP BY location, crime_type 
    HAVING COUNT(*) > 100 
    ORDER BY crime_count DESC;

  
    Location                                           Crime_type                                         Crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    On or near Unspecified                             Violence and sexual offences                       2516
    On or near Supermarket                             Shoplifting                                        2187
    On or near Parking Area                            Violence and sexual offences                       2021
    On or near Unspecified                             Shoplifting                                        1823
    On or near Unspecified                             Other theft                                        1231
    On or near Shopping Area                           Shoplifting                                        987
    On or near Supermarket                             Violence and sexual offences                       985
    On or near Unspecified                             Anti-social behaviour                              913
    On or near Unspecified                             Public order                                       882
    On or near Shopping Area                           Violence and sexual offences                       791
    
Insight: Supermarkets are high-risk locations for both shoplifting and violence. Parking areas show high rates of violence and sexual offences.

---

Question 4: Outcomes of Reported Crimes  

use West_Midlands_Crime
    
    SELECT last_outcome_category, COUNT(*) AS outcome_count
    FROM CombinedCrimeData
    GROUP BY last_outcome_category;

    last_outcome_category                                                                                                                                                                                                                                            outcome_count
    ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- -------------
    Under investigation                                                                                                                                                                                                                                              36479
    Action to be taken by another organisation                                                                                                                                                                                                                       2315
    Awaiting court outcome                                                                                                                                                                                                                                           5487
    NULL                                                                                                                                                                                                                                                             13497
    Local resolution                                                                                                                                                                                                                                                 1658
    Suspect charged as part of another case                                                                                                                                                                                                                          3
    Unable to prosecute suspect                                                                                                                                                                                                                                      72397
    Status update unavailable                                                                                                                                                                                                                                        1412
    Offender given a caution                                                                                                                                                                                                                                         596
    Further action is not in the public interest                                                                                                                                                                                                                     20
    Further investigation is not in the public interest                                                                                                                                                                                                              58
    Formal action is not in the public interest                                                                                                                                                                                                                      13
    Investigation complete; no suspect identified                                                                                                                                                                                                                    53475

Insight: Over 125,000 reported crimes (approximately 62%) result in either "unable to prosecute" or "no suspect identified," highlighting significant challenges in crime resolution.

---
 
Question 5: Correlation Between Crime Type and Outcome

    USE West_Midlands_Crime
    
    SELECT TOP 10 crime_type, Last_outcome_category, COUNT(*) AS outcome_count
    FROM CombinedCrimeData
    GROUP BY crime_type, Last_outcome_category
    ORDER BY outcome_count DESC

    Crime_type                                         Last_outcome_category                              outcome_count
    -------------------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- -------------
    Violence and sexual offences                       Unable to prosecute suspect                        45962
    Violence and sexual offences                       Under investigation                                13863
    Anti-social behaviour                              NULL                                               13497
    Vehicle crime                                      Investigation complete; no suspect identified      11552
    Violence and sexual offences                       Investigation complete; no suspect identified      10786
    Other theft                                        Investigation complete; no suspect identified      6302
    Criminal damage and arson                          Investigation complete; no suspect identified      6299
    Public order                                       Unable to prosecute suspect                        6112
    Burglary                                           Investigation complete; no suspect identified      5032 
    Shoplifting                                        Investigation complete; no suspect identified      4550

Insight: Violence and sexual offences have the highest rate of "unable to prosecute" outcomes. Property crimes (vehicle, theft, burglary, shoplifting) most frequently result in "investigation complete; no suspect identified."

---

Question 6: Time Trends for Specific Crime Types

    SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Criminal damage and arson'
    GROUP BY month, crime_type
    ORDER BY month;

    Month                                              Crime_type                                         Crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Criminal damage and arson                          2421
    2023-04                                            Criminal damage and arson                          2379
    2023-05                                            Criminal damage and arson                          2377
    2023-06                                            Criminal damage and arson                          2367
    2023-07                                            Criminal damage and arson                          2423
    2023-08                                            Criminal damage and arson                          2270
    
# Trend: Relatively stable throughout the period, with a slight decrease in August.

SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Anti-Social behaviour'
    GROUP BY month, crime_type
    ORDER BY month;
    month                                              crime_type                                         crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Anti-social behaviour                              1455
    2023-04                                            Anti-social behaviour                              1953
    2023-05                                            Anti-social behaviour                              2040
    2023-06                                            Anti-social behaviour                              2679
    2023-07                                            Anti-social behaviour                              2479
    2023-08                                            Anti-social behaviour                              2891
    
# Trend: Significant upward trend from March to August, nearly doubling over the six-month period.

SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Burglary'
    GROUP BY month, crime_type
    ORDER BY month;

    month                                              crime_type                                         crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Burglary                                           1810
    2023-04                                            Burglary                                           1551
    2023-05                                            Burglary                                           1509
    2023-06                                            Burglary                                           1456
    2023-07                                            Burglary                                           1528
    2023-08                                            Burglary                                           1727
    
# Trend: Decline from March to June, followed by an increase in July and August.    

SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Drugs'
    GROUP BY month, crime_type
    ORDER BY month;

    month                                              crime_type                                         crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Drugs                                              698
    2023-04                                            Drugs                                              585
    2023-05                                            Drugs                                              665
    2023-06                                            Drugs                                              635
    2023-07                                            Drugs                                              689
    2023-08                                            Drugs                                              640
    
# Trend: Relatively stable, with a notable dip in April.

SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Violence and sexual offences'
    GROUP BY month, crime_type
    ORDER BY month;

    month                                              crime_type                                         crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Violence and sexual offences                       13189
    2023-04                                            Violence and sexual offences                       12700
    2023-05                                            Violence and sexual offences                       12723
    2023-06                                            Violence and sexual offences                       12977
    2023-07                                            Violence and sexual offences                       12781
    2023-08                                            Violence and sexual offences                       11497
    
# Trend: Consistent monthly volume (12,500–13,200) until a notable drop in August.

SELECT month, crime_type, COUNT(*) AS crime_count
    FROM CombinedCrimeData
    WHERE crime_type = 'Vehicle crime'
    GROUP BY month, crime_type
    ORDER BY month;

    month                                              crime_type                                         crime_count
    -------------------------------------------------- -------------------------------------------------- -----------
    2023-03                                            Vehicle crime                                      3312
    2023-04                                            Vehicle crime                                      3291
    2023-05                                            Vehicle crime                                      2866
    2023-06                                            Vehicle crime                                      2692
    2023-07                                            Vehicle crime                                      2843
    2023-08                                            Vehicle crime                                      2697

# Trend: Gradual decline from March to June, stabilising in the summer months.

---

## Key Insights Summary

| Finding | Insight |
|---------|---------|
| **Most common location** | "Unspecified" locations account for the most crimes, followed by parking areas and supermarkets |
| **Most common crime** | Violence and sexual offences (75,867 incidents – 38% of total) |
| **Highest resolution gap** | 62% of crimes result in no suspect identified or unable to prosecute |
| **Fastest growing crime** | Anti-social behaviour nearly doubled (1,455 → 2,891) from March to August |
| **Supermarket risk** | Supermarkets are hotspots for both shoplifting (2,187) AND violence (985) |
| **August anomaly** | Violence and sexual offences dropped significantly in August (-1,284 vs July) |

## SQL Skills Demonstrated

| Skill | Application |
|-------|-------------|
| Database creation | `CREATE DATABASE` |
| Table creation with `SELECT INTO` | Combined six tables into one |
| `UNION ALL` | Consolidated monthly data without removing duplicates |
| `GROUP BY` with aggregates | Crime counts by location, type, outcome |
| `ORDER BY` with `DESC` | Top crime locations and types |
| `HAVING` clause | Filtered location-crime pairs with >100 incidents |
| `WHERE` clause | Filtered specific crime types for trend analysis |
| Handling `NULL` values | Anti-social behaviour outcomes identified as NULL |
| Date filtering | Monthly trend analysis using month column |## Key Insights Summary




