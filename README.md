# COVID-19 Vaccination SQL Analysis

A SQL portfolio project analyzing global COVID-19 deaths and vaccination data to uncover trends in infection rates, mortality, vaccination progress, and country-level pandemic impact.

This project demonstrates how SQL can be used for real-world public health data analysis, including data exploration, joins, aggregations, window functions, and business-style insight generation.

---

## Overview

COVID-19 created one of the largest global public health datasets in recent history. Countries reported daily information about cases, deaths, population, and vaccinations, making it possible to analyze how the pandemic affected different regions over time.

This project uses SQL to explore COVID-19 death and vaccination datasets. The analysis focuses on understanding infection rates, death percentages, vaccination rollout progress, and country-level comparisons.

The goal is to demonstrate practical SQL skills using a real-world dataset and convert raw health data into meaningful analytical insights.

---

## Problem Statement

Raw COVID-19 datasets contain thousands of rows across different countries, dates, and metrics. Without structured querying, it is difficult to answer important questions such as:

- Which countries had the highest infection rates?
- Which countries had the highest death counts?
- What percentage of the population was infected?
- How did vaccination progress over time?
- What percentage of people were vaccinated in each country?
- How can SQL joins and window functions be used to analyze pandemic trends?

This project answers these questions using Microsoft SQL Server.

---

## Key Features

- Exploratory SQL analysis on global COVID-19 data
- Country-level infection and death rate analysis
- Continent-level death count comparison
- Vaccination progress tracking
- Population vs vaccination percentage calculation
- SQL joins between death and vaccination datasets
- Window functions for rolling vaccination totals
- Common Table Expressions for clean query organization
- Temporary tables for reusable analysis
- Views for dashboard-ready outputs

---

## Tech Stack

| Category | Tools / Technologies |
|---|---|
| Database | Microsoft SQL Server |
| Query Language | SQL |
| IDE | SQL Server Management Studio |
| Data Source | COVID-19 deaths and vaccination datasets |
| Data Files | Excel |
| Analysis Type | Exploratory Data Analysis |
| SQL Concepts | Joins, CTEs, Temp Tables, Views, Window Functions, Aggregations |

---

## Dataset

The repository includes COVID-19 death and vaccination datasets in Excel format.

Repository files include:


covid19-vaccination-sql-analysis/
│
├── Covid19 Vaccination Data Analysis/
├── Covid19.....jpg
├── CovidDeaths.xlsx
├── CovidVaccinations.xlsx
├── Covid_Deaths_Full_Dataset.xlsx
├── My_Microsoft_SQL_Portfolio_Projects.txt
└── README.md

The dataset contains COVID-related information such as:

Country / location
Continent
Date
Population
Total cases
New cases
Total deaths
New deaths
Vaccination count
People vaccinated
Rolling vaccination totals

## Analysis Workflow
    Raw COVID-19 Excel Data
          ↓
    Import into SQL Server
          ↓
    Clean and Explore Tables
          ↓
    Analyze Cases and Deaths
          ↓
    Join Death and Vaccination Tables
          ↓
    Calculate Vaccination Progress
          ↓
    Create CTEs, Temp Tables, and Views
          ↓
    Generate Insights for Reporting

## SQL Analysis Performed
**1. Initial Data Exploration**

Basic queries were used to inspect the COVID deaths and vaccination tables.

    SELECT *
    FROM CovidDeaths
    ORDER BY location, date;
    SELECT *
    FROM CovidVaccinations
    ORDER BY location, date;

**2. Total Cases vs Total Deaths**

This analysis calculates the likelihood of death among confirmed COVID-19 cases.

    SELECT 
        location,
        date,
        total_cases,
        total_deaths,
        (total_deaths / total_cases) * 100 AS death_percentage
    FROM CovidDeaths
    WHERE location = 'India'
    ORDER BY date;

This helps answer:

What percentage of confirmed cases resulted in death?

**3. Total Cases vs Population**

This query calculates what percentage of a country’s population was infected.

    SELECT 
        location,
        date,
        population,
        total_cases,
        (total_cases / population) * 100 AS percent_population_infected
    FROM CovidDeaths
    ORDER BY location, date;

This helps compare infection spread across countries with different population sizes.

**4. Countries with Highest Infection Rate**

    SELECT 
        location,
        population,
        MAX(total_cases) AS highest_infection_count,
        MAX((total_cases / population)) * 100 AS percent_population_infected
    FROM CovidDeaths
    GROUP BY location, population
    ORDER BY percent_population_infected DESC;

This identifies countries where the largest share of the population was infected.

**5. Countries with Highest Death Count**

    SELECT 
      location,
      MAX(CAST(total_deaths AS INT)) AS total_death_count
    FROM CovidDeaths
    WHERE continent IS NOT NULL
    GROUP BY location
    ORDER BY total_death_count DESC;

This shows which countries reported the highest total COVID-19 death counts.

**6. Continent-Level Death Count**

    SELECT 
        continent,
        MAX(CAST(total_deaths AS INT)) AS total_death_count
    FROM CovidDeaths
    WHERE continent IS NOT NULL
    GROUP BY continent
    ORDER BY total_death_count DESC;

This helps compare the pandemic’s reported death impact across continents.

**7. Global Numbers by Date**

    SELECT 
        date,
        SUM(new_cases) AS total_cases,
        SUM(CAST(new_deaths AS INT)) AS total_deaths,
        SUM(CAST(new_deaths AS INT)) / SUM(new_cases) * 100 AS death_percentage
    FROM CovidDeaths
    WHERE continent IS NOT NULL
    GROUP BY date
    ORDER BY date;

This provides a global daily view of cases, deaths, and death percentage.

**8. Joining Death and Vaccination Data**

    SELECT *
    FROM CovidDeaths dea
    JOIN CovidVaccinations vac
        ON dea.location = vac.location
        AND dea.date = vac.date;

This combines death statistics and vaccination statistics using country and date.

**9. Rolling Vaccination Count**

      SELECT 
          dea.continent,
          dea.location,
          dea.date,
          dea.population,
          vac.new_vaccinations,
          SUM(CAST(vac.new_vaccinations AS BIGINT)) 
              OVER (
                  PARTITION BY dea.location 
                  ORDER BY dea.location, dea.date
              ) AS rolling_people_vaccinated
      FROM CovidDeaths dea
      JOIN CovidVaccinations vac
          ON dea.location = vac.location
          AND dea.date = vac.date
      WHERE dea.continent IS NOT NULL
      ORDER BY dea.location, dea.date;

This calculates the cumulative number of vaccinations per country over time.

**10. Vaccinated Population Percentage Using CTE**

    WITH PopVsVac AS (
        SELECT 
            dea.continent,
            dea.location,
            dea.date,
            dea.population,
            vac.new_vaccinations,
            SUM(CAST(vac.new_vaccinations AS BIGINT)) 
                OVER (
                    PARTITION BY dea.location 
                    ORDER BY dea.location, dea.date
                ) AS rolling_people_vaccinated
        FROM CovidDeaths dea
        JOIN CovidVaccinations vac
            ON dea.location = vac.location
            AND dea.date = vac.date
        WHERE dea.continent IS NOT NULL
    )
    SELECT *,
        (rolling_people_vaccinated / population) * 100 AS percent_population_vaccinated
    FROM PopVsVac;

This query calculates the percentage of each country’s population vaccinated over time.

## Key SQL Concepts Demonstrated

SQL Concept	How It Is Used
SELECT Statements	Extract relevant columns from COVID datasets
WHERE Clause	Filter by country, continent, and valid records
GROUP BY	Aggregate cases, deaths, and vaccination counts
ORDER BY	Sort results by date, country, or metric
Aggregate Functions	Calculate totals, maximums, and percentages
CAST	Convert data types for numerical calculations
JOIN	Combine deaths and vaccination tables
Window Functions	Calculate rolling vaccination totals
CTEs	Organize complex vaccination percentage queries
Temp Tables	Store intermediate analysis results
Views	Create reusable outputs for dashboarding

## Example Business / Public Health Questions Answered

This project can help answer:

Which countries had the highest infection rate compared to population?
Which countries reported the highest total deaths?
What was the death percentage over time?
How did vaccination progress by country?
What percentage of each country’s population was vaccinated?
How can SQL be used to prepare data for public health dashboards?

## How to Use This Project
**1. Clone the Repository**
git clone https://github.com/Shreevikas-BJ/covid19-vaccination-sql-analysis.git
cd covid19-vaccination-sql-analysis

**2. Open SQL Server Management Studio**

Open Microsoft SQL Server Management Studio or your preferred SQL environment.

**3. Import the Excel Files**

Import the following files into SQL Server tables:

CovidDeaths.xlsx
CovidVaccinations.xlsx
Covid_Deaths_Full_Dataset.xlsx

Recommended table names:

CovidDeaths
CovidVaccinations
CovidDeathsFullDataset

**4. Run the SQL Queries**

Open the SQL project file or copy queries from:

My_Microsoft_SQL_Portfolio_Projects.txt

Then run the queries step by step.

## Recommended Repository Description
SQL portfolio project analyzing global COVID-19 vaccination and death datasets to uncover trends, vaccination progress, and country-level insights.

## Business and Analytical Value

This project shows how SQL can be used to transform public datasets into meaningful insights. The same SQL techniques used here are directly applicable to business analytics, healthcare analytics, operations reporting, and dashboard preparation.

The project demonstrates the ability to:

Explore raw datasets
Clean and filter analytical records
Join related datasets
Calculate KPIs and percentages
Use window functions for time-based analysis
Prepare reusable SQL outputs for visualization tools

## Key Learnings

This project helped strengthen:

SQL querying fundamentals
Data exploration techniques
Aggregation and grouping logic
Percentage-based KPI calculation
SQL joins across related datasets
Window functions for cumulative metrics
CTEs and temp tables for complex analysis
Public health data interpretation

## Future Improvements

Add a Power BI or Tableau dashboard
Convert Excel datasets into CSV files for easier loading
Add a dedicated SQL script file instead of a .txt query file
Add data cleaning queries
Add views for dashboard-ready tables
Add visual screenshots of key insights
Add country filters and region-based analysis
Automate data loading using Python or SQL Server Import Wizard documentation
Add advanced time-series trend analysis

## Author

Shreevikas Bangalore Jagadish
Graduate Student, Information Technology and Management
Illinois Institute of Technology

GitHub: Shreevikas-BJ
LinkedIn: shreevikasbj
Portfolio: datascienceportfol.io/shreevikasbj

## Repository

    https://github.com/Shreevikas-BJ/covid19-vaccination-sql-analysis
