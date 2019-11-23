# First steps with MySQL

## Set up:
[Instructions](https://medium.com/@migueldoctor/run-mysql-phpmyadmin-locally-in-3-steps-using-docker-74eb735fa1fc)

1. Install docker.
2. From terminal, run the following commands:
```
	$ docker pull mysql:8.0.1
	$ docker pull phpmyadmin/phpmyadmin:latest
	$ docker version
	$ docker run --name my-own-mysql -e MYSQL_ROOT_PASSWORD=mypass123 -d mysql:8.0.1
	$ docker ps -a
	$ docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin
	$ docker ps -a
```
3. Access [http://localhost:8081/](http://localhost:8081/) from your browser.
	By default, type root as username and the password you set up in step 4 of the terminal instructions (that is p4ss in this tutorial).

[http://localhost/phpmyadmin/server_sql.php](http://localhost/phpmyadmin/server_sql.php)

Once you shut your computer down, the docker docks will be turned off. Hence, you need to build those docks up again! For that, rerun the following commands from bash:
```
	$ docker run --name my-own-mysql -e MYSQL_ROOT_PASSWORD=mypass123 -d mysql:8.0.1
	$ docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin
	$ docker ps -a
```

#### Now, let's work with our SQL local server.

For that, run the following SQL commands for creating each of the tables:
**Table people**
CREATE TABLE people (id INT, name VARCHAR (30), gender VARCHAR (1), nacionality VARCHAR (30), age INT, PRIMARY KEY (id));
**Table studies**
CREATE TABLE studies (student_id INT, subject_id INT, year INT, mark INT, PRIMARY KEY (student_id, subject_id));
**Table subjects**
CREATE TABLE subjects (id INT, name VARCHAR (30), credits INT, field VARCHAR (10), PRIMARY KEY (id) );
**Table teaches**
CREATE TABLE teaches (teacher_id INT, subject_id INT, year INT, PRIMARY KEY (teacher_id, subject_id));

Then, in this toy example, we need to upload the data (csv files) manually with the 'import' button at the top.

### Questions to solve using SQL:

1. What are the primary keys of each table?
We can check it at the user interface (UI) because the table shows a yellow key along the name of that variable that is the primary key.
Another way to do that is using the command
`DESCRIBE table;`

2. Que asignaturas hay?
`SELECT name FROM subjects;`

3. Que campos (ramas) hay en el master?
`SELECT DISTINCT field FROM subjects`

4. En que asignaturas da clase Grace Hopper?
```
SELECT DISTINCT s.name 
	FROM people p
	JOIN teaches t ON p.id = t.teacher_id
	JOIN subjects s ON s.id = t.subject_id
	WHERE p.name = 'Grace Hopper';	
```
5. Quienes son los profesores de la asignatura "Compilers"?
```
SELECT 
```
6. Quienes son los profesores de la academia?
```
SELECT 
```

7. Cuantos son los profesores de la academia?
```
SELECT 
```

8. En cuantas asignaturas participa cada profesor?
SELECT 

9. Cual es la asignatura con mas matriculas registradas?
```
SELECT ss.subject_id, s.name, COUNT (*) AS n_students
	FROM studies ss
	JOIN subject s ON s.id = ss.subject_id
	GROUP BY ss.subject_id
	ORDER BY n_students DESC;
```
10. Quien es el alumno matriculado en mas asignaturas?
SELECT 

11. Quien es el alumno matriculado en mas creditos?
SELECT 

12. Cual es la nacionalidad mas repetida entre los profesores?
SELECT 

13. Cual es la edad media de los alumnos de "Data mining"?
SELECT 

14. Cual es la nota media en "Algebra"?
SELECT 

15. Cual es la nota media en las asignaturas de "cs" (Computer science)?
SELECT 

16. Cual es la nota media en las asignaturas de "mat" (Mathematics) entre los aprobados?
SELECT 
17. Quien es el/la alumno/a con mejor nota media?
SELECT 

18. Sabiendo que un credito vale 30 EUR, cuanto ha ganado la academia cada anyo?
SELECT 

19. Que asignaturas estan monopolizadas? (solo un profesor en la asignatura)
SELECT 

20. Si los profesores ganan un 30% de los creditos que imparten, cuanto hay que pagar a cada uno?
SELECT 
