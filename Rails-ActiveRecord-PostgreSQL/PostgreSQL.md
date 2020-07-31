## Postgres

[Postgres.app page](https://postgresapp.com/)
![download instructions](https://i.imgur.com/U9YlqC1.png)
To configure your PATH after installing:
```
sudo mkdir -p /etc/paths.d &&
echo /Applications/Postgres.app/Contents/Versions/latest/bin | sudo tee /etc/paths.d/postgresapp
```

To install the pg gem, make sure you have set up your $PATH correctly, then execute the following command:
```
sudo ARCHFLAGS="-arch x86_64" gem install pg
```

###### with Rails:
In config/database.yml, use the following settings:
```
development:
    adapter: postgresql
    database: [YOUR_DATABASE_NAME]
    host: localhost
```

### SQL vs. MongoDB (or non-relational DBs)

#### What is SQL and how does it work?
SQL (Strustured Query Language) is used to communicate with a database, it is the **standard language for relational database management systems**. SQL statements are used to perform tasks such as update data on a database, or retrieve data from a database.

Terminology:
![terms](https://i.imgur.com/PCHRt4r.png)

### PostgreSQL
PostgreSQL is a powerful, open source ***object-relational database*** system that uses and extends the SQL language combined with many features that safely store and scale the most complicated data.


#### PostgreSQL System Architecture:

In database jargon, PostgreSQL uses a **client/server model**. A PostgreSQL session consists of the following cooperating processes (programs):

- A server process, which manages the database files, accepts connections to the database from client applications, and performs database actions on behalf of the clients. The database server program is called postgres.

- The user's client (frontend) application that wants to perform database operations. 

As is typical of client/server applications, the client and the server can be on different hosts and communicate over a TCP/IP network connection. The PostgreSQL server can handle multiple concurrent connections from clients by starting (“forking”) a new process for each connection. The master server process is always running, waiting for client connections, whereas client and associated server processes come and go.

#### 

### Creating, accessing, and deleting DBs:

`psql` in terminal to connect to the database associated with user's database or `-# psql -d username`
`user=#`

#### Create
To create a new database, in this example named mydb, you use the following command in the terminal:
> `$ createdb mydb`

> You could also `-# CREATE DATABASE mydb` in the `psql` CLI.

#### Access

Start up psql for the mydb database by typing the command:
> `$ psql mydb`

Alternatively, use `\c` to connect to the database after it is created.
> `\c mydb` in psql

The last line should show the following once you are connected to `mydb` 
> `mydb=#`

#### Delete
If you are the owner (creator) of the database mydb, you can destroy it using the following command:

> `$ dropdb mydb`

The App will show a visual representation of databases:
![postgr](https://i.imgur.com/5mLBB20.png)

### Warning: close out `psql` processes when done.
```
tflosse-# \q
tflosse@Tamaras-MBP ~ % exit

[Process completed]
```

[Syntax Guide](https://www.sqlstyle.guide/)

##### Above process along with several other command  illustrated below:

```sql
-- psql active
tflosse=# CREATE DATABASE mydb;
CREATE DATABASE
tflosse=# \l
-- for a list of DBs
                                     List of databases
      Name       |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
-----------------+----------+----------+-------------+-------------+-----------------------
 generalassembly | tflosse  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 postgres        | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
 template1       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
 tflosse         | tflosse  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(5 rows)

tflosse=# \c mydb
-- to connect
You are now connected to database "mydb" as user "tflosse".
generalassembly=# \d
Did not find any relations.
generalassembly=# CREATE TABLE students (
generalassembly(# id SERIAL PRIMARY KEY,
generalassembly(# first_name VARCHAR NOT NULL,
generalassembly(# last_name VARCHAR NOT NULL,
generalassembly(# quote TEXT,
generalassembly(# birthday VARCHAR);
CREATE TABLE
generalassembly=# \d
-- to show relations
               List of relations
 Schema |      Name       |   Type   |  Owner  
--------+-----------------+----------+---------
 public | students        | table    | tflosse
 public | students_id_seq | sequence | tflosse
(2 rows)

DROP TABLE students; 
-- "drop" means to delete an entire table or database
\d
DROP DATABASE mydb;
\q 
--quits
```

### Simple CRUD
#### READ queries

```sql
-- gets all values from a table
SELECT * FROM table_name;

-- gets two columns from a table
SELECT column_name, other_column FROM table_name;

-- gets two columns from a table if a certain critera is true
SELECT column_name, other_column FROM table_name WHERE some_value > 100;

-- gets two columns from a table if a certain critera is true, only returning 10 records, in descending order
SELECT column_name, other_column FROM table_name WHERE value > 100 LIMIT 10 ORDER BY DESC ;
```

#### CREATE queries
```sql
INSERT INTO students (first_name, last_name) VALUES ('Andy', 'Whitely');
-- This won't work!

INSERT INTO students (first_name, last_name, quote, birthday) VALUES ('Andy', 'Whitely', 'Two goldfish are in a tank. One says, "Know how to drive this thing?"', 'April 7');
```

#### UPDATE queries
```sql
UPDATE students SET first_name = 'Andrew' WHERE first_name = 'Andy';
```

#### DELETE queries
```sql
DELETE FROM students WHERE first_name = 'Andy';
DELETE FROM students WHERE first_name = 'Andrew';
```

[Visual explanation of queries](https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/)


