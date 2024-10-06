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
  SELECT continent,location,DATE, population,total_cases,(total_cases/population)*100 AS caseperPopulationPercent
  FROM PortfolioProject..CovidDeath
  WHERE continent = 'Africa'
  ORDER BY 2,3
  ```
