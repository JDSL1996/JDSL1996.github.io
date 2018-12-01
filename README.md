# Table of Contents  

[TOC]

# Learning SQL With SQLite3
* * *
  This tutorial is going to be a gentle introductions into learning how to write SQL queries using SQLite3  
## Before you start
  This tutorial, while being gentle, still requires basic knowledge of computers.  You will need to install applications that you will be running from a command prompt and navigate your filesystem from the command line.
## Why SQL?
  * SQL and Databases provide us with a very powerful way to represent data and to draw conclusions about that data.  Its also considered very efficient at accessing and storing data, to the point where most applications that have to store data consider databases.  
  * SQL also helps to formalize data storage, instead of having to learn how everyone stores and accesses their data you can use SQL as a uniform file access and modification language.



* * *

## Getting Sqlite3
  For this tutorial we will be using sqlite3 as our relational database management system.  
  To install and use sqlite 3 follow instructions at: https://www.tutorialspoint.com/sqlite/sqlite_installation.htm  
  The tutorial covers Linux, Mac, and Windows installations
## Data we will use
  To better ilustrate how specific statements work, we will be using an SQL file to create a database that we can queries on.
    
  SQL file: [Download](https://www.dropbox.com/s/vql9wia6vqbcq5s/Movies.sql?dl=1)
* * *
## Getting to know Sqlite3
* * *
  Different relational database management systems will have different command line interfaces and ways that you can display and output your data.  Therefore getting to know sqlite3 is useful if you want to take full advantage of the system you are using.  
### Start up arguments  
When SQL is launched you can either provide a database for it to connect to, or use an ethereal database.
  
* When connecting to a database your data will be saved in the file you specify.  
```shell
  sqlite3 file.db
```
* When you do not specify a database, it will use a temporary database in which any changes are not saved locally.  
```shell
  sqlite3
```
To make a new database file, create an empty file and change its extension to .db 

### Special Commands
Some special commands that are useful to use with sqlite and their function are shown in the table below

|Command name|Command function                                                    |
|------------|--------------------------------------------------------------------|
|.header     |returns queries with the table header                               |
|.help       |returns a list of more commands that can be useful                  |
|.mode       |determines how queries look/are formatted when returned             |
|.read       |reads in sql file with path relative to where sqlite3 was launched  |
|.schema     |give you the schema of all tables, or a specified table.            |
|.save       |you can use save to save a temporary database using .save filename  |
|.table      |shows the current tables in the database                            |
|.exit       |exits sqlite3                                                       |

### Loading Movies.sql into sqlite3
The easiest way to load Movies.sql is to navigate to where it is in the filesystem and then launch sqlite3 with no file provided, as shown below.
```shell
  sqlite3
```


You will get then get the following:
```shell
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
```
  

Within sqlite3 we can then use the .read command to read in the sql file.
```shell
sqlite>.read Movies.sql
```
  

To see the tables we can now use .table, which will yield:
```shell
sqlite>.table
Movie     Rating    Reviewer
```
  

We now have data in our database, if we want to save this to a new file we can now use .save to do so
```shell
sqlite>.save Movies.db
```

Since the file is now saved as Movies.db we can exit and use Movies.db as a startup argument for sqlite3 to load the data we just loaded!
```shell
sqlite>.exit

>sqlite3 Movies.db

SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
sqlite> .table
Movie     Rating    Reviewer
```

### Sqlite3 Result Formatting
Select is a way that we can get data, one of the most basic SELECT SQL queries is
```
sqlite>SELECT * FROM Movie;
```
Which then returns:
  
  
```
101|Gone with the Wind|1939|Victor Fleming
102|Star Wars|1977|George Lucas
103|The Sound of Music|1965|Robert Wise
104|E.T.|1982|Steven Spielberg
105|Titanic|1997|James Cameron
106|Snow White|1937|
107|Snow White|1997|Michael Cohn
108|Avatar|2009|James Cameron
109|Raiders of the Lost Ark|1981|Steven Spielberg
```
We've just selected all elements in the table movies, but the formatting isn't really friendly for our eyes and we don't really know from looking at the data what each column represents.  
  
sqlite3 provides formatting options through .header and .mode that will solve this problem for us!
  
  
```
sqlite>.header on
sqlite>.mode column
sqlite>SELECT * FROM Movie;
mID         title               year        director
----------  ------------------  ----------  --------------
101         Gone with the Wind  1939        Victor Fleming
102         Star Wars           1977        George Lucas
103         The Sound of Music  1965        Robert Wise
104         E.T.                1982        Steven Spielbe
105         Titanic             1997        James Cameron
106         Snow White          1937
107         Snow White          1997        Michael Cohn
108         Avatar              2009        James Cameron
109         Raiders of the Los  1981        Steven Spielbe
```
As you might have seen, Raiders of the Lost Arc is cut off, and so is Steven Spielberg.  
This is due to the default width selected by sqlite3. However, if you like you can specify the width manually with .width
  
  
```
sqlite>.width 5 25 4 20
sqlite>SELECT * FROM Movie;
mID    title                      year  director
-----  -------------------------  ----  --------------------
101    Gone with the Wind         1939  Victor Fleming
102    Star Wars                  1977  George Lucas
103    The Sound of Music         1965  Robert Wise
104    E.T.                       1982  Steven Spielberg
105    Titanic                    1997  James Cameron
106    Snow White                 1937
107    Snow White                 1997  Michael Cohn
108    Avatar                     2009  James Cameron
109    Raiders of the Lost Ark    1981  Steven Spielberg
```  
Things can start to get pretty fiddly though since you have to specify the width again if you wanted to get it perfect for another table.
To reset the widths just use:
  
  
```
sqlite>.width 0 0 0 0

```

* * *
## SQL Basics
* * *
  With formatting out of the way, now we'll talk about specific SQL queries and the way they are structured!  
  
### SELECT
  SELECT statements are fundamental to SQL, allowing us to do very advanced queries to get very specific sets of data.  
  One basic structure of a SELECT query is: SELECT COLUMN FROM TABLE  
  SELECT * FROM Movie is really saying "Select all columns from the table Movie".  However you can also do:  

```
sqlite> SELECT mID FROM Movie;
mID
----------
101
102
103
104
105
106
107
108
109
```
With the english translation being "Select column mID from the table Movie"
  
To select multiple columns you seperate with a "," like so:
```
sqlite> SELECT mID,director FROM Movie;
mID         director
----------  --------------
101         Victor Fleming
102         George Lucas
103         Robert Wise
104         Steven Spielbe
105         James Cameron
106
107         Michael Cohn
108         James Cameron
109         Steven Spielbe
```


However, really there a many many ways in which you can do SELECT queries, you can see this in [This flow graph](https://www.sqlite.org/lang_select.html) of a SELECT statement
### WHERE
WHERE is used to get data based on some conditional expression one type of expression is a direct reference to data in a column, for example:
```
sqlite> SELECT * FROM Movie WHERE mID > 106;
mID         title       year        director    
----------  ----------  ----------  ------------
107         Snow White  1997        Michael Cohn
108         Avatar      2009        James Camero
109         Raiders of  1981        Steven Spiel
```
This says "Select all columns from the movie table where the value in the column named mID is greater than 106."

While SELECT is used to get large amounts of data WHERE can be used to reduce the data to only containing the information desired.

Unlike SELECT, WHERE does not have a flow graph however, expression does which shows the complete functional capabilities of WHERE. [Here is that graph](https://www.sqlite.org/lang_expr.html)

### DELETE
DELETE can be used to remove entire rows form a table using the WHERE functionality seen previously.

Using the same example as WHERE, if we for some reason wanted to remove all movies with mID greater than 106 we would simply put:
```
sqlite> DELETE FROM Movie WHERE mID > 106;
```
This statement will not return anything but after it is run we can check it worked by using SELECT:
```
sqlite> SELECT * FROM Movie;
mID         title               year        director      
----------  ------------------  ----------  --------------
101         Gone with the Wind  1939        Victor Fleming
102         Star Wars           1977        George Lucas  
103         The Sound of Music  1965        Robert Wise   
104         E.T.                1982        Steven Spielbe
105         Titanic             1997        James Cameron 
106         Snow White          1937                    
```
Only movies with mID less than or equal to 106 remain in the table.
DELETE also [Has a flow graph](https://www.sqlite.org/lang_delete.html)
### INSERT  
INSERT is used to add an entire row. Columns can be specified followed by values or if and entire row is being made you can also just add the values in order without specifying column.

An example of specifying both with associated SELECT to show the progress:
```
sqlite> INSERT INTO Movie(
   ...> mID, year)
   ...> VALUES(
   ...> 100,1900);
sqlite> SELECT * FROM Movie;
mID         title               year        director      
----------  ------------------  ----------  --------------
101         Gone with the Wind  1939        Victor Fleming
102         Star Wars           1977        George Lucas  
103         The Sound of Music  1965        Robert Wise   
104         E.T.                1982        Steven Spielbe
105         Titanic             1997        James Cameron 
106         Snow White          1937                      
107         Snow White          1997        Michael Cohn  
108         Avatar              2009        James Cameron 
109         Raiders of the Los  1981        Steven Spielbe
100                             1900                      
```
The [flow graph for INSERT](https://www.sqlite.org/lang_insert.html)
### UPDATE  
UPDATE can change the values in an associated column using WHERE and expression statements seen previously:
```
sqlite> SELECT * FROM Movie;
mID         title               year        director      
----------  ------------------  ----------  --------------
101         Gone with the Wind  1939        Victor Fleming
102         Star Wars           1977        George Lucas  
103         The Sound of Music  1965        Robert Wise   
104         E.T.                1982        Steven Spielbe
105         Titanic             1997        James Cameron 
106         Snow White          1937                      
107         Snow White          1997        Michael Cohn  
108         Avatar              2009        James Cameron 
109         Raiders of the Los  1981        Steven Spielbe
sqlite> UPDATE Movie SET year = 1987 WHERE mID = 101;
sqlite> SELECT * FROM Movie;
mID         title               year        director      
----------  ------------------  ----------  --------------
101         Gone with the Wind  1987        Victor Fleming
102         Star Wars           1977        George Lucas  
103         The Sound of Music  1965        Robert Wise   
104         E.T.                1982        Steven Spielbe
105         Titanic             1997        James Cameron 
106         Snow White          1937                      
107         Snow White          1997        Michael Cohn  
108         Avatar              2009        James Cameron 
109         Raiders of the Los  1981        Steven Spielbe
```
Here the year was updated to 1987 for the movie with mID of 101
The [flow graph for UPDATE](https://www.sqlite.org/lang_update.html)
