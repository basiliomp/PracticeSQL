# First steps with MySQL

## Set up
[Instructions](https://medium.com/@migueldoctor/run-mysql-phpmyadmin-locally-in-3-steps-using-docker-74eb735fa1fc)

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

Once you shut your computer down, the docker docks will be turned off. Hence, you need to build those docks up again! For that, rerun the following commands from bash:

```bash
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
Another way to do that is using the command
```DESCRIBE table;```

2. Que asignaturas hay?
```SELECT name FROM subjects;```

3. Que campos (ramas) hay en el master?
```SELECT DISTINCT field FROM subjects```

4. En que asignaturas da clase Grace Hopper?
```sql
SELECT DISTINCT s.name 
	FROM people p
	JOIN teaches t ON p.id = t.teacher_id
	JOIN subjects s ON s.id = t.subject_id
	WHERE p.name = 'Grace Hopper';	
```
5. Quienes son los profesores de la asignatura "Compilers"?
```sql
SELECT 
```
6. Quienes son los profesores de la academia?
```sql
SELECT 
```

7. Cuantos son los profesores de la academia?
```sql
SELECT 
```

8. En cuantas asignaturas participa cada profesor?
```sql
SELECT 
```
9. Cual es la asignatura con mas matriculas registradas?
```sql
SELECT ss.subject_id, s.name, COUNT (*) AS n_students
	FROM studies ss
	JOIN subject s ON s.id = ss.subject_id
	GROUP BY ss.subject_id
	ORDER BY n_students DESC;
```
10. Quien es el alumno matriculado en mas asignaturas?
```sql
-- Good solution

-- Subquery method
SELECT new_studies.student_id COUNT (*) AS n_subjects
	FROM (SELECT DISTINCT ss.student_id, ss.subject_id FROM Studies ss) AS new_studies
	JOINpeople p ON p.id = new_studies.student_id
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
11. Quien es el alumno matriculado en mas creditos?
```sql
SELECT 
```

12. Cual es la nacionalidad mas repetida entre los profesores?
```sql
SELECT 
``` 

13. Cual es la edad media de los alumnos de "Data mining"?
```sql
SELECT 
```

14. Cual es la nota media en "Algebra"?
```sql
SELECT 
```

15. Cual es la nota media en las asignaturas de "cs" (Computer science)?
```sql
SELECT 
```

16. Cual es la nota media en las asignaturas de "mat" (Mathematics) entre los aprobados?
```sql
SELECT
```

17. Quien es el/la alumno/a con mejor nota media?
```sql
SELECT 
```

18. Sabiendo que un credito vale 30 EUR, cuanto ha ganado la academia cada anyo?
```sql
SELECT 
```

19. Que asignaturas estan monopolizadas? (solo un profesor en la asignatura)
```sql
SELECT 
```

20. Si los profesores ganan un 30% de los creditos que imparten, cuanto hay que pagar a cada uno?
```sql
SELECT 
```
