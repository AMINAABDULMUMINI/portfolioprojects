select *
from portfoliaproject1..coviddeaths
order by 3,4

--select *
--from portfoliaproject1..covidvaccinations
--order by 3,4

select location, date,total_cases, new_cases, total_deaths, population
from portfoliaproject1..coviddeaths
order by 1,2

--looking at total cases vs total deaths
--shows likelihood of dying if one contract covid in a country

select location, date, total_cases, total_deaths, case
when (total_deaths IS NOT null and total_deaths <> '' )
and (total_cases is not null and total_cases <> '' )
and total_cases <>0
and total_deaths <>0
then cast(total_deaths as float)/total_cases *100
else 0
end AS death_percentage
from portfoliaproject1..coviddeaths
where location like '%states%'
order by 1,2;

--looking at total cases vs population
--shows what percentage of population got covid

select location, date, total_cases, population, case
when (population IS NOT null and population <> '' )
and (total_cases is not null and total_cases <> '' )
and total_cases <>0
and population <>0
then cast(total_cases as float)/population*100
else 0
end AS percentpopulationinfected
from portfoliaproject1..coviddeaths
where location like '%states%'
order by 1,2;


--looking at countries with highest infection rate compared to population


SELECT 
    location,
    population,
    MAX(total_cases) AS highestinfectedcount,
    MAX(CAST(total_cases AS FLOAT) / population) * 100 AS percent_population_infected
FROM 
    portfoliaproject1..coviddeaths
--WHERE 
--    location LIKE '%states%'
GROUP BY 
    location, population
	order by percent_population_infected desc

	---breaking things by continent

	--showing continents with the highest death count per population

select location, max(cast(total_deaths as int)) as totaldeathcount
from portfoliaproject1..coviddeaths
--where location like '%states%'
where continent is not null
group by location
order by totaldeathcount desc




--global numbers

SELECT date,
    SUM(CAST(new_cases AS int)) AS total_cases,
    SUM(CAST(new_deaths AS int)) AS total_deaths,
    CASE 
        WHEN SUM(CAST(new_cases AS int)) = 0 THEN 0
        ELSE (SUM(CAST(new_deaths AS int)) * 100.0 / NULLIF(SUM(CAST(new_cases AS int)), 0))
    END AS death_percentage
FROM 
    portfoliaproject1..coviddeaths
WHERE
    continent IS NOT NULL
GROUP BY 
    date
ORDER BY 1, 2 asc;

--looking at total population vs vaccinations

SELECT dea.continent, dea.location, dea.date, dea.population,
       ISNULL(NULLIF(vac.new_vaccinations, ''), 'null') AS new_vaccinations,
       SUM(CASE WHEN ISNULL(NULLIF(vac.new_vaccinations, ''), '') = 'null' THEN 0 ELSE CONVERT(BIGINT, vac.new_vaccinations) END) 
           OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS rollingpeoplevaccinated
FROM portfoliaproject1..coviddeaths dea
LEFT JOIN portfoliaproject1..covidvaccinations vac ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
  AND vac.new_vaccinations IS NOT NULL
  AND dea.continent <> ''
ORDER BY 2,3;

--use CTES


WITH Popvsvac (continent, location, date, population, new_vaccinations, rollingpeoplevaccinated) AS (
    SELECT dea.continent, dea.location, dea.date, dea.population,
           ISNULL(NULLIF(vac.new_vaccinations, ''), 'null') AS new_vaccinations,
           SUM(CASE WHEN ISNULL(NULLIF(vac.new_vaccinations, ''), '') = 'null' THEN 0 ELSE CONVERT(BIGINT, vac.new_vaccinations) END) 
               OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS rollingpeoplevaccinated
    FROM portfoliaproject1..coviddeaths dea
    LEFT JOIN portfoliaproject1..covidvaccinations vac ON dea.location = vac.location AND dea.date = vac.date
    WHERE dea.continent IS NOT NULL
      AND vac.new_vaccinations IS NOT NULL
      AND dea.continent <> ''
	  --order by 2,3
)
SELECT *,
       (CAST(rollingpeoplevaccinated AS FLOAT) / population) * 100 AS vaccination_percentage
FROM Popvsvac;



	  --Temp table

	 
IF OBJECT_ID('tempdb..#TempPopVsVac') IS NOT NULL
    DROP TABLE #TempPopVsVac;

CREATE TABLE #TempPopVsVac (
    continent VARCHAR(255),
    location VARCHAR(255),
    date DATE,
    population BIGINT,
    new_vaccinations VARCHAR(50),
    rollingpeoplevaccinated BIGINT
);

WITH Popvsvac (continent, location, date, population, new_vaccinations, rollingpeoplevaccinated) AS (
    SELECT dea.continent, dea.location, dea.date, dea.population,
           ISNULL(NULLIF(vac.new_vaccinations, ''), 'null') AS new_vaccinations,
           SUM(CASE WHEN ISNULL(NULLIF(vac.new_vaccinations, ''), '') = 'null' THEN 0 ELSE CONVERT(BIGINT, vac.new_vaccinations) END) 
               OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS rollingpeoplevaccinated
    FROM portfoliaproject1..coviddeaths dea
    LEFT JOIN portfoliaproject1..covidvaccinations vac ON dea.location = vac.location AND dea.date = vac.date
    WHERE dea.continent IS NOT NULL
      AND vac.new_vaccinations IS NOT NULL
      AND dea.continent <> ''
)
INSERT INTO #TempPopVsVac (continent, location, date, population, new_vaccinations, rollingpeoplevaccinated)
SELECT continent, location, date, population, new_vaccinations, rollingpeoplevaccinated
FROM Popvsvac;

SELECT * FROM #TempPopVsVac;


























