# First steps with MySQL

This is a repository for SQL practice exercises.

In this README file I will list the steps needed to create a database for later work. First we need to take care of *Docker* and the appropiate containers, and then create the database in our local server (second section of this document).  
The exercises are addressed in another file named [PracticeSQL.md](https://github.com/basiliomp/PracticeSQL/blob/master/PracticeSQL.md) within this same repository.
 
## Set up: Docker

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
	docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin # See WSL2 note below
	docker ps -a
```

**Note for WSL2 users**: Running Docker on the Windows Subsystem for Linux 2, consider specifying the address as `-p 0.0.0.0:8080:81`. This will make the server run by the container accessible from Windows.


3. Access [http://localhost:8081/](http://localhost:8081/) from your browser.
	By default, type root as username and the password you set up in step 4 of the terminal instructions (that is mypass123 in this tutorial).

[http://localhost/phpmyadmin/server_sql.php](http://localhost/phpmyadmin/server_sql.php)

Once you shut your computer down, the docker docks will be turned off. Hence, you need to build those docks up again! For that, rerun the following commands from bash. Note that the remove command needs the container id of to run:

Try calling the command ``docker restart`` with an explicit call to the containers' names. (You can see those containers ids with the command ``docker ps -a``.) Here is an example:
```bash
    docker restart ff87492168ef
```

If that failed, then remove the containers and run them again.
```bash
	docker ps -a	
	docker rm 2ed8b04dc463
	docker run --name my-own-mysql -e MYSQL_ROOT_PASSWORD=mypass123 -d mysql:8.0.1
	docker run --name my-own-phpmyadmin -d --link my-own-mysql:db -p 8081:80 phpmyadmin/phpmyadmin # See WSL2 note above.
	docker ps -a
```

##  Set up: Creating a database in SQL local server.

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
CREATE TABLE people (id INT, name VARCHAR (30), gender VARCHAR (1), nationality VARCHAR (30), age INT, PRIMARY KEY (id));
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
