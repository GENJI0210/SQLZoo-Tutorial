# [SELECT within SELECT Tutorial](https://www.sqlzoo.net/wiki/SELECT_within_SELECT_Tutorial)

This tutorial looks at how we can use SELECT statements within SELECT statements to perform more complex queries.

| name        | continent | area    | population | gdp          |
| :---------- | :-------- | :------ | :--------- | :----------- |
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
| ...         |           |         |            |              |

1. **List each country** **name** **where the** **population** **is larger than that of 'Russia'.**

   ```sql
   SELECT name FROM world
   WHERE population > (SELECT population FROM world WHERE name='Russia')
   ```

2. **Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.**

   ```sql
   SELECT name FROM world
   WHERE continent = 'Europe' AND gdp/population > (
   SELECT gdp/population FROM world WHERE name = 'United Kingdom'
   )
   ```

3. **List the** **name** **and** **continent** **of countries in the continents containing either** **Argentina** **or** **Australia****. Order by name of the country.**

   ```sql
   SELECT name, continent FROM world
   WHERE continent IN (SELECT continent FROM world WHERE name IN ('Argentina', 'Australia'))
   ORDER BY name
   ```

4. **Which country has a population that is more than United Kingdom but less than Germany? Show the name and the population.**

   ```sql
   SELECT name, population FROM world
   WHERE population > (SELECT population FROM world WHERE name = 'United Kingdom') AND population < (SELECT population FROM world WHERE name = 'Germany')
   ```

5. Germany (population roughly 80 million) has the largest population of the countries in Europe. Austria (population 8.5 million) has 11% of the population of Germany.

   Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.

   ```sql
   SELECT name, concat(cast(round(100*population/(SELECT population FROM world WHERE name='Germany'),0)AS int), '%') FROM world
   WHERE continent = 'Europe'
   ```

6. **Which countries have a GDP greater than every country in Europe? [Give the** **name** **only.] (Some countries may have NULL gdp values)**

   ```sql
   SELECT name FROM world
   WHERE gdp > ALL (SELECT gdp FROM world 
                    WHERE continent = 'Europe' AND gdp IS NOT NULL)
   ```

7. Find the largest country (by area) in each continent, show the **continent**, the **name** and the **area**:

   The above example is known as a **correlated** or **synchronized** sub-query.

   ```sql
   SELECT continent, name, area FROM world x
   WHERE area >= ALL (SELECT area 
                      FROM world y 
                      WHERE y.continent=x.continent AND population>0)
   ```

8. **List each continent and the name of the country that comes first alphabetically.**

   ```sql
   SELECT continent, name FROM world
   GROUP BY continent
   ```

9. **Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show** **name****,** **continent** **and** **population****.**

   ```sql
   SELECT name, continent, population 
   FROM world x 
   WHERE 25000000 > ALL(SELECT population 
                        FROM world y 
                        WHERE y.continent = x.continent 
                        )
   ```

10. **Some countries have populations more than three times that of each of their neighbours (in the same continent). Give the countries and continents.**

    ```sql
    SELECT name, continent FROM world x
    WHERE population >= ALL (select 3*population from world y 
                             where x.continent = y.continent and population > 0 and x.name <> y.name)
    ```