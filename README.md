# First steps with MySQL

## Set up 

[Instructions](https://medium.com/@migueldoctor/run-mysql-phpmyadmin-locally-in-3-steps-using-docker-74eb735fa1fc)
True
1. **Install docker**. This is feasible for MacOS, Linux distributions and Windows 10 Professional. If your system is not compatible, install additional software. For example in Windows 10 Home, install and run WAMP. Then access phpAdmin at the url:
[http://localhost/phpmyadmin/](http://localhost/phpmyadmin/).

2. From terminal, run the following commands:
```bash
	docker pull mysql:8.0.1
	docker pull phpmyadmin/phpmyadmin:latest
	docker version
	docker run --name my-own-mysql -e MYSQL_ROOT_PASSWORD=mypass123 -d mysql:8.0.1
	docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin
	docker ps -a
```
3. Access [http://localhost:8081/](http://localhost:8081/) from your browser.
	By default, type root as username and the password you set up in step 4 of the terminal instructions (that is mypass123 in this tutorial).

[http://localhost/phpmyadmin/server_sql.php](http://localhost/phpmyadmin/server_sql.php)

Once you shut your computer down, the docker docks will be turned off. Hence, you need to build those docks up again! For that, rerun the following commands from bash. Note that the remove command needs the container id of to run:

Try calling the command ``restart`` with an explicit call to the containers' names. (You can see those containers ids with the command ``docker ps -a``.)
```bash
    docker restart 2ed8b04dc463
    docker restart ff87492168ef
```

If that failed, then remove the containers and run them again.
```bash
	docker ps -a	
	docker rm 2ed8b04dc463
	docker rm ff87492168ef
	docker run --name my-own-mysql -e MYSQL_ROOT_PASSWORD=mypass123 -d mysql:8.0.1
	docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin
	docker ps -a
```

## Now, let's start by creating a database in SQL local server.

The task of building a database, can be decomposed in the following steps:
1. First we need to think of all the entities we want.
2. Think and justify the number of tables we need to represent that data base.
3. The command for actually creating it would be ``CREATE DATABASE name_of_the_database ;``

Data bases can be organised in **normalised or denormalised** ways.
**Denormalised** schemes are better for consuling data since every bit of information is saved in several tables. Its main problems lay in the usage of more space (sin tables are bigger, becuase the have more columns) and the potential lack of consistency! 
Since values referred to the same variable (registered in different colums of different tables!) need to be updated or modified at the same time, problems may arise when we do not update the variable in all the tables as we should. This, then, originates a consistency problem. 

For example, the column ADDRESS may be present in two tables, but only updated in one of them because of a hypothetical problem. Which address to use then?

**Normalised** schemes consist of several tables with non-overlapping variables (columns) apart from the key. It makes consulting data more costly because the system needs to check through several tables to compile all information requested. On the other hand, this scheme is better for consistency, as we have just seen.

The reasoning behind relational data bases: relational algebra.
[https://en.wikipedia.org/wiki/Relational_algebra]

Example of query for creating an empty table:
```sql
CREATE TABLE people (id INT NOT NULL, name VARCHAR (64), gender CHAR (1), nationality VARCHAR (30), age INT, PRIMARY KEY (id))
```
This creates a table called 'people'
It contains the columns (variables):
 - ``id``: set to be an integer variable ``(INT)`` which does not accept NULL values ``(NOT NULL)``
 - ``name``: set to be a character variable of variable length ``(VARCHAR)`` with a maximum length of 64.
 - ``gender``: set to be a character variable of fixed length 1 ``(CHAR (1))``.
 - ``nationality``:set to be a character variable of variable length ``(VARCHAR)``
 - ``age``: set to be an integer variable ``(INT)``
 Finally, the command ``PRIMARY KEY (id)`` sets the column id to be the primary key. This impedes id to store NULL values (hence the ``NOT NULL`` specification becomes redundant) and makes it mandatory to have unique values.

The following SQL commands will create each of the tables needed for our exercises:
**Table people**
```sql
CREATE TABLE people (id INT, name VARCHAR (30), gender VARCHAR (1), nacionality VARCHAR (30), age INT, PRIMARY KEY (id));
```
**Table studies**
```sql
CREATE TABLE studies (student_id INT, subject_id INT, year INT, mark INT, PRIMARY KEY (student_id, subject_id));
```
**Table subjects**
```sql
CREATE TABLE subjects (id INT, name VARCHAR (30), credits INT, field VARCHAR (10), PRIMARY KEY (id) );
```
**Table teaches**
```sql
CREATE TABLE teaches (teacher_id INT, subject_id INT, year INT, PRIMARY KEY (teacher_id, subject_id));
```

Then, in this toy example, we need to upload the data (csv files without the name row) manually with the 'import' button at the top.

## First tasks to solve using SQL:

Query example for consulting names in the table people for those entries with a value in age larger than 20 and gender equal to 'F' (i.e. female):
```sql
SELECT name FROM people WHERE age > 20 AND gender = 'F'
```
```sql
ALTER TABLE people ADD COLUMN phone VARCHAR (10);
```
Difference between ``DROP`` and ``DELETE``? ``DELETE`` changes the data, while ``DROP`` modifies the data and the data structure (with ``DROP table;`` the table no longer exists).
Difference between ``EDIT`` and ``ALTER``? Similar
```sql
SELECT DISTINCT
SELECT age FROM people ORDER BY variable ASC; (for ascending sorting)
```
The two following queries are equivalent! (``BETWEEN`` takes inclusive values at the limits)
```sql
SELECT name FROM people WHERE age >= 20 OR age <= 30;
SELECT name FROM people WHERE age BETWEEN 20 AND 30;
```
Some basic statistics calculations (average, count, standard deviation, maximun and minimum in this example):
```sql
SELECT AVG(age), COUNT(age), STD(age), MAX(age), MIN(age) FROM people;
```
Grouping and group by calculations
```sql
SELECT nationality, AVG(age) FROM people GROUP BY nationality;
```
Filters over groups (not files!) need the operator HAVING:
```sql
SELECT nationality, AVG(age) FROM people GROUP BY nationality HAVING AVG(age) > 30;
```
**Join and alias**
The letter after the name of the table creates an alias for that table that we can later call, and specify variables in that table with a dot (p.id).
This is the first query presented in more than one line because of its complexity.
```sql
SELECT * FROM people p
	JOIN studies s ON p.id = s.student_id 
	JOIN subjects sb ON s.subject_id = sb.id;
```
If nothing is specified about the JOIN, it will be executed as an inner join (that is the case above).

## Questions to solve using SQL:

1. What are the primary keys of each table?
We can check it at the user interface (UI) because the table shows a yellow key along the name of that variable that is the primary key.
Another way to do that is using the following command with each table:
```DESCRIBE table;```

2. What are the subjects registered?
```SELECT DISTINCT (name) FROM subjects;```

3. Which fields are taught?
```SELECT DISTINCT field FROM subjects;```

4. Which subject is taught by Grace Hopper?
```sql
SELECT p.name, s.name
    FROM people as p
    JOIN teaches as t ON p.id = t.teacher_id 
    JOIN subjects as s ON t.subject_id = s.id
    WHERE p.name = 'Grace Hopper';
```
5. Who are the teacher for the subject 'Compilers'?
```sql
SELECT y.year, p.name
	FROM people as p
    LEFT JOIN teaches as t ON p.id = t.teacher_id
    LEFT JOIN subjects as s ON t.subject_id = s.id
    WHERE s.name = 'Compilers';
```
There were Grace Hooper in 2016 and Dennis Richtie in 2017.

6. Who are the teachers at this centre? (Answer for last year in database).
```sql
SELECT DISTINCT t.year, p.name
    FROM people as p
    LEFT JOIN teaches as t ON p.id = t.teacher_id
    WHERE t.year = (SELECT MAX(year) FROM teaches);
```

7. How many teachers work at this centre? (Answer for last year in database)
```sql
SELECT COUNT(DISTINCT p.name)
    FROM people as p
    LEFT JOIN teaches as t ON p.id = t.teacher_id
    WHERE t.year = (SELECT MAX(year) FROM teaches);
```
10 in 2017.

8. How many subjects are related to each teacher? 
```sql
SELECT teacher_id, COUNT(subject_id) AS n_subjects
	FROM teaches
    WHERE year = 2017
    GROUP BY(teacher_id)
    ORDER BY n_subjects DESC;
```
9. What is the subject with more students registered?
```sql
SELECT s1.name, COUNT(DISTINCT s2.student_id) AS n_students
	FROM subjects AS s1
    LEFT JOIN studies AS s2
    ON s1.id = s2.subject_id
    GROUP BY s1.name
    ORDER BY n_students DESC
    LIMIT 3;

Advanced programming with 20 students in 2017.

--SELECT ss.subject_id, s.name, COUNT (*) AS n_students
--	FROM studies ss
--	JOIN subject s ON s.id = ss.subject_id
--	GROUP BY ss.subject_id
--	ORDER BY n_students DESC;
```
10. Who is the student signed up for more subjects?
```sql
-- Good solution
SELECT p.name, COUNT(DISTINCT s.subject_id) AS n_subjects
	FROM people AS p
    LEFT JOIN studies AS s
    ON p.id = s.student_id
    GROUP BY p.name
    ORDER BY n_subjects DESC
    LIMIT 3;
```
Pierre-Simon Laplace and Christiaan Huygens, both with 9 subjects.

```sql
-- Subquery method
SELECT new_studies.student_id COUNT (*) AS n_subjects
	FROM (SELECT DISTINCT ss.student_id, ss.subject_id FROM Studies ss) AS new_studies
	JOIN people p ON p.id = new_studies.student_id
	GROUP BY new_studies.student_id
	ORDER BY n_subjects DESC

SELECT ss.student_id, COUNT(*) AS n_registers
	FROM Studies ss
	JOIN people p ON p.id = ss.student_id
	GROUP BY ss.student_id
	ORDER BY n_registers DESC LIMIT 1;

-- Proposed solution:
SELECT ss.student_id, COUNT(*) AS n_registers
	FROM Studies ss
	JOIN people p ON p.id = ss.student_id
	GROUP BY ss.student_id
	ORDER BY n_registers DESC LIMIT 1;
```
11. Who is the student registered with more credit hours?
```sql
SELECT p.name, SUM(s2.credits) AS n_credits
	FROM people AS p
    LEFT JOIN studies AS s
    ON p.id = s.student_id
    LEFT JOIN subjects AS s2
    ON s.subject_id = s2.id
    GROUP BY p.name
    ORDER BY n_credits DESC
    LIMIT 1;
```
Christiaan Huygens with 135 credits.

12. What is the most frequent nationality among teachers?
```sql
SELECT p.nationality
	FROM people AS p
    INNER JOIN teaches AS t
    ON p.id = t.teacher_id
    GROUP BY p.nationality
    ORDER BY p.nationality DESC
    LIMIT 3;
``` 
Swiss

13. What is the average age of people studying "Data mining"?
```sql
SELECT AVG(p.age) AS avg_age
	FROM people AS p
    INNER JOIN studies AS s1
    ON p.id = s1.student_id
    INNER JOIN subjects AS s2
    ON s1.subject_id = s2.id
    WHERE s2.name = 'Data mining';
```
25.9 years.

14. What is the average grade in "Algebra"?
```sql
SELECT AVG(s2.mark)
	FROM subjects AS s1
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    WHERE s1.name = "Algebra";
```
5.8

15. What is the average grade for Computer science subjects ('cs')?
```sql
SELECT AVG(s2.mark) AS average_grade
	FROM subjects AS s1
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    WHERE s1.field = "cs";
```
For the total of subjects in that field, the average grade is 6.5.

If we want to know the average grade for *each* subject in the Computer Sciences field, the adecuate query would be:
```sql
SELECT s1.name, AVG(s2.mark) AS average_grade
	FROM subjects AS s1
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    WHERE s1.field = "cs"
    GROUP BY s1.name;
```
With the following result:

| name | average_grade |
|----------------------|---------------|
| Intro to programming | 5.3889 |
| Compilers | 6.2000 |
| Algorithms | 6.6429 |
| Hardware | 6.4667 |
| Computer science | 7.4444 | 
| Computer networks | 7.3846 |
| Artificial Intelligence |	6. 4444 |
| Advanced programming | 6.8000 |

16. What is the average grade for "mat" (Mathematics) among those that passed?
```sql
SELECT AVG(s2.mark) AS average_grade
	FROM subjects AS s1
    JOIN studies AS s2
    	ON s1.id = s2.subject_id
    WHERE s1.field LIKE '%mat%'
    	AND s2.mark >= 5;
```
For all the students and all subjects, the average grade in matemathics subjects is 7.2.

17. Who is the student with the best average grade?
```sql
SELECT p.name, AVG(s.mark) AS avg_grade
	FROM people AS p
    LEFT JOIN studies as s
    ON p.id = s.student_id
    GROUP BY p.id
    ORDER BY avg_grade DESC
    LIMIT 3;
```
Nicolaus Copernicus and Richard Stallman both with 8.5.

18. Each credit-hour costs €30 on application, so how much money earned the learning centre each year?
```sql
SELECT s2.year, SUM(s1.credits) * 30 AS income
	FROM subjects AS s1
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    GROUP BY s2.year;
```

19. Which subjects are taught by one teacher only?
```sql
SELECT s1.name, COUNT( DISTINCT t.teacher_id) AS n_teachers
	FROM subjects AS s1
    JOIN teaches AS t
    ON s1.id = t.subject_id
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    GROUP BY s1.name
    HAVING n_teachers < 2;

-- Another take on the problem, considering subjects over different years:
SELECT s1.name, s2.year, COUNT( DISTINCT t.teacher_id) AS n_teachers
	FROM subjects AS s1
    LEFT JOIN teaches AS t
     ON s1.id = t.subject_id
    LEFT JOIN studies AS s2
     ON s1.id = s2.subject_id
    GROUP BY s2.year, s1.name
    HAVING n_teachers < 2;
-- The solution is still the same.
```
Artificial intelligence and Gaulois theory. 

20. If teachers were to earn a 30% of the cost of their subjects, how much should each of them be paid?
```sql
SELECT t.teacher_id, s2.year, SUM(s1.credits) * 30 AS annual_salary
	FROM subjects AS s1
    JOIN studies AS s2
    ON s1.id = s2.subject_id
    JOIN teaches AS t
    ON s1.id = t.subject_id
    GROUP BY s2.year, t.teacher_id
    ORDER BY annual_salary DESC;
```
