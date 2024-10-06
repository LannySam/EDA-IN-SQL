# EDA_Covid
## Project Objective
This project aim to perform an Exploratory Data Analysis into the COVID 19 dataset with a focal point on the Africa Continent. 
## Dataset
The dataset was downloaded from [Our World Data](https://ourworldindata.org/coronavirus) in CSV format. The Dataset contains 67 columns and 365,526 rows that holds information about number of cases, death, hospitalization and vaccination between Jan. 2020 to Dec. 2023.

## SQL Server
- MS SQL server
- MS Excel
## Data Preparation and Modelling
- The dataset was loaded using MS Excel and was divided into two seperate tables namely *CovidDeath* and *CovidVaccinations*. CovidDeath contains information about the number of cases and deaths recorded worldwide, while CovidVac contan info about Covid Vaccination.
- The two tables was loaded into MS SQl Server using the portfolioproject DB.
## Exploratory Data Analysis
The data set was analyzed to solve the follwoing questions
- What is the total number of cases recorded?
- What is the percentage of death to population?
- What is the infection rate in each country compare to population?
- how is the vaccination rate compared to death and infection rate?

## Data Analysis
1. The first part of the analysis involve analyzing the number of cases and death accross the continent of Africa. 
- Viewing the Whole dataset
  ``` SQL
  -- Viewing the whole dataset
  SELECT *
  FROM PortfolioProject.dbo.CovidDeath
  ORDER BY 3,4
  ```
- Selecting the appropriate data
  ``` SQL
  --- Selecting the data needed for exploratory analysis
  SELECT location, DATE, total_cases, new_cases, total_deaths, population
  FROM PortfolioProject..CovidDeath
  ORDER BY 1,2
  ```
- Cases Vs Population
  ``` SQL
  -- Total Cases vs Population
  -- Showing the percentage of the population that was infected with Covid
  SELECT continent, location, DATE, population, total_cases, (total_cases/population)*100 AS caseperPopulationPercent
  FROM PortfolioProject..CovidDeath
  WHERE continent = 'Africa'
  ORDER BY 2,3
  ```
- Cases Vs Death
  ```SQL
  -- Total Cases vs Total Deaths
  -- Shows the percentage of death in relation to the number of cases
  SELECT continent, location, DATE, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathperCasesPercent
  FROM PortfolioProject.dbo.CovidDeath
  WHERE continent = 'Africa'
  ORDER BY 2,3
  ```
- Infection Rate per Countries
  ```SQL
  -- Countries with Highest infection rate compared to population
  -- This compared the rate of infection to the population of eah country
  SELECT location, Max(total_cases) As Infected_populace,	population,
    Max((total_cases/population))*100 as PercentPopulationInfected
  FROM PortfolioProject..CovidDeath
  GROUP BY location, population
  ORDER BY PercentPopulationInfected Desc
  ```
- Death Count per Countries
  ```SQL
  -- Countries with highest death count per population
  --note this is when the continent is not null
  SELECT location,
		Max(total_deaths) As TotalDeathCount		
  FROM PortfolioProject..CovidDeath
  WHERE continent = 'Africa'
  GROUP BY location
  ORDER BY TotalDeathCount Desc
  ```
- Total cases and death in the Continent of Africa
  ```SQL
  -- Calculating the total numbers of death and caese
  -- The death to case percent
  SELECT --DATE,
	SUM(new_cases) as Sum_cases,
	SUM(new_deaths) as sum_deaths,
	SUM(new_deaths)/nullif (SUM(new_cases),0)*100 as DeathPercentage
  FROM PortfolioProject..CovidDeath
  WHERE continent = 'Africa'
  --GROUP BY date
  ORDER BY 1,2
  ```
2. The second part of the analysis include analyzing the CovidVaccination table alog with CovidDeath tables, using *Joins*, *CTE* and *Temptables*.
- Population Vs Vaccination
```SQL
-- Joining the Vaccination table with death table
-- Total Population vs Vaccination
-- using partition by to sum the new_vaccinations daily
SELECT dea.continent, dea.location,dea.date, dea.population, vac.new_vaccinations,
	SUM(Convert(bigint, vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) AS sum_daily_new_vaccinations
FROM PortfolioProject..CovidDeath dea
JOIN PortfolioProject..CovidVaccination vac
	on dea.location = vac.location
	and dea.date = vac.date
WHERE dea.continent = = 'Africa'
ORDER BY 2,3
```
- Using CTE to calculate vaccination percent to Population
  ```SQL
  -- CTE
  With POPvsVAC (Continent, Loacation, Date, population, new_vaccinations, sum_daily_new_vaccinations)
  AS
  (
  SELECT dea.continent, dea.location,dea.date, dea.population, vac.new_vaccinations,
  	SUM(Convert(bigint, vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) AS sum_daily_new_vaccinations
  FROM PortfolioProject..CovidDeath dea
  JOIN PortfolioProject..CovidVaccination vac
	on dea.location = vac.location
	and dea.date = vac.date
  WHERE dea.continent = 'Africa'
  )
  SELECT *, (sum_daily_new_vaccinations/population)*100 AS PopulanceVaccinatedPercent
  FROM POPvsVAC
  ORDER BY 2,3
  ```
