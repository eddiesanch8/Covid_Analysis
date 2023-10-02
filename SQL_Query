--- READ ME --- 
These are examples of queries made after uploading the following public dataset to BigQuery: https://ourworldindata.org/covid-deaths



This is a personal project

--- READ ME --- 


-- 1. Covid Case Fatality 
  a. Code that can be used to find the percentage of Covid cases that ended in patient deaths
  b. This code can be used to look at the percentage of deaths based off of location. Just substitute the WHERE 
  clause to equal any location. WHERE location LIKE %states% can help find United States or just typing united states also works. 

SELECT  
  location,
  total_cases,
  total_deaths, 
  (total_deaths/total_cases *100) AS percentage_of_deaths

FROM `sandboxplay-396500.capstone_project.covid_deaths` 
WHERE location = "Zimbabwe"
ORDER BY 
 location


 -- 2. Population Infected 
  a. Code looks at the max amount and the percentage of people infected with the country with the highest infection rate at the top 
  of the query. 
  b. Code looks at the highest infection count by Country 

SELECT  
  location, 
  population, 
  MAX(total_cases) AS max_infection_count,
  MAX(total_cases/population *100) AS percentage_infected 

FROM `sandboxplay-396500.capstone_project.covid_deaths` 
GROUP BY 
  location, population 
ORDER BY 
  percentage_infected desc



 -- 3. Continents with the highest death count allegedly 

SELECT 
  location,
  MAX(total_deaths) AS TotalDeathCount
FROM `sandboxplay-396500.capstone_project.covid_deaths` 
WHERE
  continent is null 
GROUP BY
  location 
ORDER BY
  TotalDeathCount desc


-- 4. New Cases and Total Deaths
  a. Code is used to create 3 new columns for different data analysis 
  b. Allows for nondestructive data cleaning for days in which there are no new cases
SELECT  
    date,  
    SUM(new_deaths) as Total_New_Deaths, 
    sum(new_cases) AS Total_New_Cases, 
    sum(new_deaths)/sum(new_cases)*100 as DeathPercentage
FROM `sandboxplay-396500.capstone_project.covid_deaths` 
WHERE 
  continent is not null AND new_deaths>0 AND new_cases>0 
GROUP BY 
   date
ORDER by 
1, 2


  
-- 5. Rolling Count of Vaccinations 
  a. Code allows us to join two tables in order to create a rolling count of new vaccinations per country 
  b. The column new_vaccinations is preferred over total_vaccinations because it is a daily integer that can be used to create a rolling count through partioning 

SELECT dea.continent, 
  dea.location, 
  dea.date, 
  dea.population, vac.new_vaccinations, 
  SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location order by dea.location, dea.date) as rolling_vac_count
FROM `sandboxplay-396500.capstone_project.covid_deaths` as dea

JOIN `sandboxplay-396500.capstone_project.covid_vaccinations` as vac
  ON dea.location = vac.location
  AND dea.date = vac.date
WHERE dea.continent  IS NOT NULL AND dea.continent IS NOT NULL AND vac.new_vaccinations is not null

ORDER BY 
2, 3 
  
  

  
 6. Common Table Expression (CTE) for Percent of Population Vaccinated  
   a. Uses a CTE to do further analysis of a query like finding the percentage of a newly created column (rolling_vac_count)
   b. Can be used to do a deeper dive on the data already queried 

  WITH PopVsVac 
  AS 
  (
 
  SELECT 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations, 
SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location order by dea.location, dea.date) as rolling_vac_count

  FROM `sandboxplay-396500.capstone_project.covid_deaths` as dea

  JOIN `sandboxplay-396500.capstone_project.covid_vaccinations` as vac
  ON dea.location = vac.location
  AND dea.date = vac.date
  WHERE dea.continent  IS NOT NULL AND dea.continent IS NOT NULL AND vac.new_vaccinations is not null

  ORDER BY location, date
    )
   
  SELECT *, (rolling_vac_count/population) *100 as percent_vaccinated
  FROM PopVsVac

-- 7. Creating View for Further Analysis 
  

  CREATE VIEW `sandboxplay-396500.capstone_project.percent_population_vaccinated` as 

SELECT dea.continent, 
  dea.location, 
  dea.date, 
  dea.population, vac.new_vaccinations, 
  SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location order by dea.location, dea.date) as rolling_vac_count

FROM `sandboxplay-396500.capstone_project.covid_deaths` as dea

JOIN `sandboxplay-396500.capstone_project.covid_vaccinations` as vac    
ON dea.location = vac.location
AND dea.date = vac.date

WHERE 
dea.continent IS NOT NULL AND dea.continent IS NOT NULL AND vac.new_vaccinations is not null

ORDER BY 
  2, 3 
  
