http://campus.codeschool.com/courses/the-sequel-to-sql

https://github.com/codeschool/WatchUsBuild-MovieListingsAppWithSQL

```
# Common Aggregate Functions
SELECT count(columName)
FROM tableName;

SELECT sum(columName)
FROM tableName;

SELECT avg(columName)
FROM tableName;

SELECT max(columName)
FROM tableName;

SELECT min(columName)
FROM tableName;


# Aggregates Within Clauses

SELECT columnName, aggregate_function(columnName)
FROM tableName
GROUP BY columnName;


SELECT columnName, aggregate_function(columnName)
FROM tableName
GROUP BY columnName
HAVING count(*) > 1;


SELECT columnName, aggregate_function(columnName)
FROM tableName
WHERE columnName operator value (optional)
GROUP BY columnName;
HAVING aggregate_function(columnName) operator value;


SELECT genre, sum(cost)
FROM Movies
WHERE cost >= 1000000
GROUP BY genre;
HAVING COUNT(*) > 1;

i.e.
SELECT country, sum(salary)
From Actors
WHERE role = 'supporting'
GROUP BY country
HAVING count(country) > 1;



# Constraints

CREATE TABLE Promotions
(
  id int,
  name varchar(50),
  category varchar(15)
);

INSERT INTO Promotions (id, category)
VALUES (3, 'Mechandies');



CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL,
  category varchar(15)
);

INSERT INTO Promotions (id, category)
VALUES (3, 'Mechandies');

Why use Constraints?
Prevent NULL values
Ensure column values are unique
Provide Additional validations


CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL UNIQUE,
  category varchar(15)
);


CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL,
  category varchar(15),
  CONSTRAINT uniqueName UNIQUE (name)
);

# Column Constraint
CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL UNIQUE,
  category varchar(15)
);

# Table Constraint
CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL,
  category varchar(15),
  CONSTRAINT uniqueName UNIQUE (name)
);


# Both name and category are unique
# And cannot be in duplicate in multiple rows
CREATE TABLE Promotions
(
  id int,
  name varchar(50) NOT NULL,
  category varchar(15),
  CONSTRAINT uniqueName UNIQUE (name, category)
);


# Primary Key: NOT NULL and unique
CREATE TABLE Promotions
(
  id int PRIMARY KEY,
  name varchar(50),
  category varchar(15)
);

Q: Difference b/t Primary Key vs NOT NULL and UNIQUE
A: The PRIMARY KEY can only be defined once per table, whereas NOT NULL and UNIQUE can be used on multiple columns.

#Example
CREATE TABLE Movies (
  name varchar(50) NOT NULL,
  language varchar(50),
  CONSTRAINT name_lang UNIQUE (name, language)
);


Common naming convention
Singular version of the table, an underscore followed by the columnName.
i.e: movie_id

Foreign key


SELECT id
From Movies
WHERE title = 'Gone With the Wind';

SELECT name, category
From Promotions
WHERE movie_id = 2;


CREATE TABLE Movies (
  id int PRIMARY KEY,
  title varchar(20) NOT NULL UNIQUE
);

CREATE TABLE Promotions (
  id int PRIMARY KEY,
  movie_id int REFERENCES movies(id),
  name varchar(50),
  category varchar(15)
);

CREATE TABLE Promotions (
  id int PRIMARY KEY,
  movie_id int REFERENCES movies,
  name varchar(50),
  category varchar(15)
);
When column name is missing from `REFERENCES`, primary key of that table will be used.

CREATE TABLE Promotions (
  id int PRIMARY KEY,
  movie_id int,
  name varchar(50),
  category varchar(15),
  FOREIGN KEY (movie_id) REFERENCES movies
);


i.e.
CREATE TABLE Actors (
  id int PRIMARY KEY,
  name varchar(50) NOT NULL UNIQUE,
  country_id int REFERENCES Countries
);

i.e.
CREATE TABLE Actors (
  id int PRIMARY KEY,
  name varchar(50) NOT NULL UNIQUE,
  country_id int,
  FOREIGN KEY (country_id) REFERENCES Countries
);



# Orphan record
1. DELETE FROM Movies WHERE id = 6;
2. DROP TABLE Movies;


DELETE FROM Promotions WHERE movie_id = 6;
DELETE FROM Movies WHERE id = 6;

# CHECK Constraint
CREATE TABLE Movies (
  id int PRIMARY KEY,
  title varchar(20) NOT NULL UNIQUE,
  duration int CHECK (duration > 0)
);

i.e.
CREATE TABLE Actors (
  id int PRIMARY KEY,
  name varchar(50) NOT NULL UNIQUE,
  salary integer CHECK (salary > 500),
  bonus integer CHECK (bonus < salary),
  country_id int REFERENCES Countries(id)
);


Normalization
Normalization is the process of reducing duplication in database tables

Rules
1. Tables must not contain repeating groups of data in 1 column
2. Table must not contain redunance (unnecessary repeating information)

Join table naming convention
i.e. Movies_Genres


SELECT name
FROM Genres
WHERE id = 2 or id = 3;

SELECT name
FROM Genres
WHERE id in (2,3);


Join Table
CREATE TABLE Actors_Movies (
  actor_id int,
  movie_id int
);

CREATE TABLE Actors_Movies (
  actor_id int REFERENCES Actors
  movie_id int REFERENCES Movies
);

CREATE TABLE Actors_Movies (
  actor_id int,
  movie_id int,
  FOREIGN KEY (actor_id) REFERENCES Actors,
  FOREIGN KEY (movie_id) REFERENCES Movies
);

CREATE TABLE Actors_Movies (
  actor_id int,
  movie_id int,
  FOREIGN KEY (actor_id) REFERENCES Actors,
  FOREIGN KEY (movie_id) REFERENCES Movies
);

INSERT INTO Actors_Movies (actor_id, movie_id)
  VALUES (2, 5);


# Relationships
* One to One
* One to Many
* Many to Many

Question: In our theatre 1 movie can play in one room at a time. Let's add a constraint that will ensure that no movies are ever marked to play in the same room at the same time.

CREATE TABLE Rooms (
  id int PRIMARY KEY,
  seats int,
  movie_id int UNIQUE,
  FOREIGN KEY (movie_id) REFERENCES Movies
);

CREATE TABLE Rooms (
  id int PRIMARY KEY,
  seats int,
  movie_id int REFERENCES Movies UNIQUE
);

# Inner Join

SELECT review, movie_id
FROM Reviews;

SELECT title
From Movies
WHERE id IN (1,2,4);

We can write above 2 queries into 1 query

SELECT *
FROM Movies
INNER JOIN Reviews
ON Movies.id = Reviews.movie_id;

SELECT *
FROM Reviews
INNER JOIN Movies
ON Reviews.movie_id = Movies.id;


SELECT Movies.title, Reviews.review
FROM Movies
INNER JOIN Reviews
ON Movies.id = Reviews.movie_id;


SELECT Movies.title, Genres.name
FROM Movies
INNER JOIN Movies_Genres
ON Movies.id = Movies_Genres.movie_id
INNER JOIN Genres
ON Movies_Genres.genre_id = Genres.id
WHERE Movies.title = "Peter Pan";


Q: Join the Movies table with the Rooms table so that we only fetch movies that have an associated room.
SELECT *
From Movies
INNER JOIN Rooms
ON Movies.id = Rooms.movie_id;

Q: Let's get a little more specific, and only return the movie title, the id for the room, and number of seats in the theatre.
SELECT *
From Movies
INNER JOIN Rooms
ON Movies.id = Rooms.movie_id;


SELECT Movies.title, Rooms.id, Rooms.seats
From Movies
INNER JOIN Rooms
ON Movies.id = Rooms.movie_id
WHERE Rooms.seats > 75;

SELECT Movies.title, Rooms.id, Rooms.seats
From Movies
INNER JOIN Rooms
ON Movies.id = Rooms.movie_id
WHERE Rooms.seats > 75
ORDER BY Rooms.seats DESC;

SELECT Actors.name, Movies.title
FROM Actors
INNER JOIN Actors_Movies
ON Actors.id = Actors_Movies.actor_id
INNER JOIN Movies
ON Movies.id = Actors_Movies.movie_id
ORDER BY Movies.title;

# Aliases
SELECT Movies.title AS films, Reviews.review AS reviews
FROM Movies
INNER JOIN Reviews
ON Movies.id = Reviews.movie_id;

## Without AS
SELECT Movies.title films, Reviews.review reviews
FROM Movies
INNER JOIN Reviews
ON Movies.id = Reviews.movie_id;

SELECT Movies.title "Weekly Movies", Reviews.review "Weekly Reviews"
FROM Movies
INNER JOIN Reviews
ON Movies.id = Reviews.movie_id;

SELECT m.title "Weekly Movies", Reviews.review
FROM Movies m
INNER JOIN Reviews
ON m.id = Reviews.movie_id
ORDER BY m.title;

SELECT m.title, r.review
FROM Movies m
INNER JOIN Reviews r
ON m.id = r.movie_id
ORDER BY m.title;

# OUTER JOIN
SELECT *
FROM Movies
LEFT OUTER JOIN Reviews
ON Movies.id = Reviews.movie_id;

SELECT m.title, r.review
FROM Movies m
LEFT OUTER JOIN Reviews r
ON m.id = r.movie_id
ORDER BY r.id;

SELECT *
FROM Movies
RIGHT OUTER JOIN Reviews
ON Movies.id = Reviews.movie_id;

SELECT m.title, r.review
FROM Movies m
RIGHT OUTER JOIN Reviews r
ON m.id = r.movie_id
ORDER BY r.id;

# Sub-queries
Inner query or sub-query is surrounded by paratheseses

SELECT SUM(sales)
FROM Movies
WHERE id IN
(SELECT movie_id
  FROM Promotions
  WHERE category = 'Non-cach');

Sub-query: easier to read
JOIN query: better for performance

Sub-query Syntax:
WHERE <FIELD> IN (<subquery>)
WHERE <FIELD> NOT IN (<subquery>)

SELECT *
FROM Movies
WHERE duration > (SELECT AVG(duration) From Movies);

SELECT r.id, m.title
FROM Rooms r
LEFT OUTER JOIN Movies m
ON r.movie_id = m.id
WHERE seats > (SELECT AVG(seats) FROM Rooms);
```