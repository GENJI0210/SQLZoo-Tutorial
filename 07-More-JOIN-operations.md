# [More JOIN operations](https://www.sqlzoo.net/wiki/More_JOIN_operations)

This tutorial introduces the notion of a join. The database consists of three tables `movie` , `actor` and `casting` .

1. List the films where the **yr** is 1962 [Show **id**, **title**]

   ```sql
   SELECT id, title
   FROM movie
   WHERE yr=1962
   ```

2. Give year of 'Citizen Kane'.

   ```sql
   SELECT yr FROM movie
   WHERE title = 'Citizen Kane'
   ```

3. List all of the Star Trek movies, include the **id**, **title** and **yr** (all of these movies include the words Star Trek in the title). Order results by year.

   ```sql
   SELECT id, title, yr FROM movie
   WHERE title LIKE "%Star Trek%"
   ORDER BY yr
   ```

4. What **id** number does the actor 'Glenn Close' have?

   ```sql
   SELECT id FROM actor
   WHERE name='Glenn Close'
   ```

5. What is the **id** of the film 'Casablanca'

   ```sql
   SELECT id FROM movie
   WHERE title='Casablanca'
   ```

6. Obtain the cast list for 'Casablanca'.

   what is a cast list?

   Use **movieid=11768**, (or whatever value you got from the previous question)

   ```sql
   SELECT name FROM actor
   WHERE id in
   (SELECT actorid FROM casting
   WHERE movieid=11768)
   
   #-------------OR------------
   
   SELECT actor.name FROM actor
   JOIN casting ON actor.id=casting.actorid
   WHERE movieid=11768
   ```

7. Obtain the cast list for the film 'Alien'

   ```sql
   SELECT name FROM actor
   WHERE actor.id in (
   SELECT actorid FROM casting
   WHERE movieid = (
   SELECT movie.id FROM movie 
   WHERE title = 'Alien'))
   
   #-------------OR------------
   
   SELECT actor.name FROM actor
   JOIN casting ON actor.id=casting.actorid
   JOIN movie ON movie.id=movieid
   WHERE movie.title='Alien'
   
   ```

8. List the films in which 'Harrison Ford' has appeared

   ```sql
   SELECT movie.title FROM movie 
   JOIN casting ON movie.id=casting.movieid
   JOIN actor ON actor.id=casting.actorid
   WHERE actor.name='Harrison Ford'
   ```

9. List the films where 'Harrison Ford' has appeared - but not in the starring role. [Note: the **ord** field of casting gives the position of the actor. If ord=1 then this actor is in the starring role]

   ```sql
   SELECT movie.title FROM movie 
   JOIN casting ON movie.id=casting.movieid
   JOIN actor ON casting.actorid=actor.id
   WHERE actor.name='Harrison Ford' AND casting.ord <> 1
   ```

10. List the films together with the leading star for all 1962 films.

    ```sql
    SELECT movie.title, actor.name FROM movie
    JOIN casting ON movie.id=casting.movieid
    JOIN actor ON casting.actorid=actor.id
    WHERE movie.yr=1962 AND casting.ord=1
    ```

11. Which were the busiest years for 'Rock Hudson', show the year and the number of movies he made each year for any year in which he made more than 2 movies.

    ```sql
    SELECT yr,COUNT(title) FROM movie 
    JOIN casting ON movie.id=movieid
    JOIN actor ON actorid=actor.id
    WHERE name='Rock Hudson'
    GROUP BY yr
    HAVING COUNT(title) > 2
    ```

12. List the film title and the leading actor for all of the films 'Julie Andrews' played in.

    ```sql
    SELECT DISTINCT b.title, a.name leading_star
    FROM
    (
      SELECT movie.title, actor.name, casting.movieid
      FROM movie
      JOIN casting ON movie.id = casting.movieid
      JOIN actor ON casting.actorid = actor.id
      WHERE casting.ord = 1
    ) AS a
    JOIN
    (
      SELECT movie.title, casting.movieid
      FROM movie
      JOIN casting ON movie.id = casting.movieid
      JOIN actor ON casting.actorid = actor.id
      WHERE actor.name = 'Julie Andrews'
    ) AS b
    ON a.movieid = b.movieid
    ```

13. Obtain a list, in alphabetical order, of actors who've had at least 15 **starring** roles.

    ```sql
    SELECT a.name
    FROM
    (
      SELECT actor.name, COUNT(casting.movieid) star_count
      FROM actor
      JOIN casting ON actor.id = casting.actorid
      WHERE casting.ord = 1
      GROUP BY actor.name
    ) AS a
    WHERE star_count >=15
    ORDER BY a.name
    ```

14. List the films released in the year 1978 ordered by the number of actors in the cast, then by title.

    ```sql
    SELECT movie.title, COUNT(*) cast_size FROM movie
    JOIN casting ON movie.id = casting.movieid
    WHERE movie.yr = 1978
    GROUP BY movie.title
    ORDER BY cast_size DESC, movie.title
    ```

15. List all the people who have worked with 'Art Garfunkel'.

    ```sql
    SELECT DISTINCT actor.name FROM actor
    JOIN casting ON actor.id = casting.actorid
    JOIN
    (
      SELECT casting.movieid
      FROM actor
      JOIN casting ON actor.id = casting.actorid
      WHERE actor.name = 'Art Garfunkel'
    ) AS a
    ON casting.movieid = a.movieid
    WHERE actor.name <> 'Art Garfunkel'
    ```