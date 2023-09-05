# COVID-19 Data Exploration project

## Overview
The purpose of this project was to explore several large datasets with an interesting background and that is why I chose to investigate some of the early COVID-19 infection rates and vaccination data throughout the world.

## Tools

#### Excel
Excel was used to get an initial overview of the data and to perform some minor data cleaning.

#### SQL
SQL was used to analyse the data in depth and extract the data necessary for visualization.

#### Tableau
Tableau was used to visualise the dataset.

## Data

The data used is was from ...

## Cleaning the Data

## Exploring the Data

## Exporting Data

`code`
SELECT SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
WHERE continent is not null 
--GROUP BY date
ORDER BY 1,2

SELECT location, SUM(cast(new_deaths as int)) as TotalDeathCount
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
WHERE continent is null 
AND location not in ('World', 'European Union', 'International')
GROUP BY location
ORDER BY TotalDeathCount desc

SELECT Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
GROUP BY Location, Population
ORDER BY PercentPopulationInfected desc

SELECT Location, Population,date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
GROUP BY Location, Population, date
ORDER BY PercentPopulationInfected desc

## Visualising the Data
