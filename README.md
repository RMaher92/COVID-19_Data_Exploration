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

```
-- Data Exploration

SELECT *
FROM `fine-loader-397409.CovidVaccinations.CovidVaccinations`
WHERE continent is not null
ORDER BY 3,4

-- SELECT *
-- FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
-- ORDER BY 3,4

-- Select Data that we are going to be using

SELECT location, date, total_cases, new_cases, total_deaths, population
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
ORDER by 1,2

-- Looking at total Cases vs Total Deaths
-- Shows the likelihook of dying if you contract COVID in Australia

SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
WHERE location = 'Australia'
ORDER by 1,2

-- Loooking at the Total Cases vs Population
-- Shows what percentage of population contracted COVID

SELECT location, date, population, total_cases, (total_cases/population)*100 as PercentPopulationInfected
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
-- WHERE location = 'Australia'
ORDER by 1,2

-- Looking at Countries with highest infection rate compared to population

SELECT location, population, MAX(total_cases) as HighestInfetionCount, MAX((total_cases/population))*100 as PercentPopulationInfected
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
-- WHERE location = 'Australia'
GROUP BY location, population
ORDER by PercentPopulationInfected desc

-- Showing countries with the highest death count per population

SELECT location, MAX(total_deaths) as TotalDeathCount
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
-- WHERE location = 'Australia'
WHERE continent is not null
GROUP BY location
ORDER by TotalDeathCount desc

-- Total deaths by continent

SELECT continent, MAX(total_deaths) as TotalDeathCount
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
-- WHERE location = 'Australia'
WHERE continent is not null
GROUP BY continent
ORDER by TotalDeathCount desc

-- Global Numbers - Percentage of deaths compared to total cases on each day

SELECT date, SUM(new_cases) as Total_Cases, SUM(new_deaths) as Total_Deaths, SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
--WHERE location = 'Australia'
WHERE continent is not null
GROUP BY date
ORDER by 1,2

-- Global Numbers - Percentage of deaths compared to cases total

SELECT SUM(new_cases) as Total_Cases, SUM(new_deaths) as Total_Deaths, SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
FROM `fine-loader-397409.CovidDeaths.CovidDeaths`
WHERE continent is not null
ORDER by 1,2

-- Join tables based on location and date

SELECT *
FROM `fine-loader-397409.CovidDeaths.CovidDeaths` dea
JOIN `fine-loader-397409.CovidVaccinations.CovidVaccinations` vac
 ON dea.location = vac.location
 AND dea.date = vac.date

-- Looking at total population vs vaccinations with rolling count

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) as RollingPeopleVaccinated
FROM `fine-loader-397409.CovidDeaths.CovidDeaths` dea
JOIN `fine-loader-397409.CovidVaccinations.CovidVaccinations` vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent is not null
ORDER BY 2,3

-- Use CTE

WITH PopvsVac
as
(
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) as RollingPeopleVaccinated
FROM `fine-loader-397409.CovidDeaths.CovidDeaths` dea
JOIN `fine-loader-397409.CovidVaccinations.CovidVaccinations` vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent is not null
)
SELECT *, (RollingPeopleVaccinated/population)*100
FROM PopvsVac 

-- Creating Temp table

CREATE TEMP TABLE PercentPopulationVaccinated
(
 continent string,
 location string,
 date datetime,
 population float64,
 new_vaccinations float64,
 AccumVaccinations float64
);

INSERT INTO PercentPopulationVaccinated
SELECT dea.continent, dea.location, dea.date, dea.population,vac.new_vaccinations
, SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location,dea.date) AS RollingPeopleVaccinated
FROM `fine-loader-397409.CovidDeaths.CovidDeaths` dea
JOIN `fine-loader-397409.CovidVaccinations.CovidVaccinations` vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent IS NOT NULL;

SELECT *, (AccumVaccinations/population)*100
FROM PercentPopulationVaccinated;

-- Relavant data for visulisation

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From `fine-loader-397409.CovidDeaths.CovidDeaths`
where continent is not null 
--Group By date
order by 1,2

Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
From `fine-loader-397409.CovidDeaths.CovidDeaths`
Where continent is null 
and location not in ('World', 'European Union', 'International')
Group by location
order by TotalDeathCount desc

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From `fine-loader-397409.CovidDeaths.CovidDeaths`
Group by Location, Population
order by PercentPopulationInfected desc

Select Location, Population,date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From `fine-loader-397409.CovidDeaths.CovidDeaths`
Group by Location, Population, date
order by PercentPopulationInfected desc
```

## Exporting Data

```SELECT SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
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
```

## Visualising the Data
