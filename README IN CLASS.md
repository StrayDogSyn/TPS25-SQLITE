# TPS25-SQL

Instructor: Dylan Comerford (dylancomerford1@gmail.com)

Throughout the class, there's practice prompts for you to respond to that will start with "STUDENTS:"

When you see this, please type an answer into the chat 

STUDENTS: What is one app that you use every day that you think probably relies on a database? and what may go wrong if all of that data is stored in one single spreadsheet?

# Background: What is a relational database and what is the point of using one? 

Scenario: You're working for a company that tracks global weather data. Every day, the system you created collects readings from thousands of weather stations around the world — temperature, humidity, wind speed, and more. The data collected needs to be stored somewhere. 

Option (bad): Storing it all in one massive spreadsheet:

| Station Name | City   | Country | Latitude | Longitude | Date       | Temp | Wind  | Humidity |
|--------------|--------|---------|----------|-----------|------------|------|-------|----------|
| Station A    | NYC    | USA     | 40.71    | -74.01    | 2025-05-19 | 68°F | 12mph | 70%      |
| Station A    | NYC    | USA     | 40.71    | -74.01    | 2025-05-20 | 70°F | 8mph  | 65%      |
| Station B    | Tokyo  | Japan   | 35.68    | 139.69    | 2025-05-19 | 75°F | 10mph | 60%      |


Problems:
- Station info is repeated for every row, 
- Easy to introduce inconsistent data (e.g., "NYC" vs "New York"), 
- One change (e.g., GPS correction) must be made in every row 

Soultion: Use a relational database 
- Instead of repeating information, we split it into related tables:

table `stations`
| station_id | name      | city   | country | latitude | longitude |
|------------|-----------|--------|---------|----------|-----------|
| 1          | Station A | NYC    | USA     | 40.71    | -74.01    |
| 2          | Station B | Tokyo  | Japan   | 35.68    | 139.69    |

table `weather_readings`
| reading_id | station_id | date       | temperature | wind_speed | humidity |
|------------|------------|------------|-------------|------------|----------|
| 1          | 1          | 2025-05-19 | 68°F        | 12mph      | 70%      |
| 2          | 1          | 2025-05-20 | 70°F        | 8mph       | 65%      |
| 3          | 2          | 2025-05-19 | 75°F        | 10mph      | 60%      |

Now: 
- Station info is stored once
- We link readings using station_id
- If a station moves or is renamed, we update one row, not hundreds

We can use a SQL Query to edit and analyze this data easily. 

The power of SQL allows us to draw the follwoing city-level insights from potentially thousands of readings with 4 simple lines of code:

| city  | AVG(temperature) |
|-------|------------------|
| NYC   | 69.0             |
| Tokyo | 75.0             |


```sql
SELECT city, AVG(temperature)
FROM weather_readings wr
JOIN stations s ON wr.station_id = s.station_id
GROUP BY city;
```

**Qualities of a relational database**

Notice how the first column of the stations table and weather reading table are unique numeric values. All tables in a relational database have this and it's called the **primary key**. 

Primary keys 
- Guarantee uniqueness for each row 
- Enable relationships between tables 
- Improves query process (fast and precise to reference)


table 1: `people`
| personid | name         | age |
|----------|--------------|-----|
| 1        | Alex Smith   | 30  |
| 2        | Jamie Patel  | 45  |
| 3        | Alex Smith   | 17  | 

STUDENTS: why is it important that we have a primary key in this table?

✅


You may wonder, why do we need a primary key to diferentate these 2 people when there's other qualities like age or maybe DOB that could do that too?

Benefits of a primary key:

- Performance: faster to reference numeric values 
- Stability: other fields can change
- Privacy: can keep track of sensitive information with a code 

table 2: `legal` : contains legal events (a peron may have more than 1)
| legal_event_id | personid | charge        |
|----------------|----------|---------------|
| 101            | 1        | Trespassing   |
| 102            | 2        | Petty Theft   |
| 103            | 1        | Vandalism     |

Notice how the legal events table contains the personids, attributing each event to a person.

When a primary key from one table appears in another for reference, it's called a **Foriegn Key** (ex. station_id in the weather table, personid in the legal table)

Background section question: 

As we saw above, tables in a relational database are split into smaller topics instead of storing all of the information in one table. 

STUDENTS: let's say you are making an app for a library and so you need to design a database to store information. What are some tables that you may make as a part of this database?

✅

---
Course structure for the 3 SQL courses:

1. Exploring an existing database 
2. Learning how to create a database 
3. Analytical project using the `JTCsql.db` database, where you'll use what you've learned to create an analysis of new diversion pilot programs. 
4. With your next instructor, you'll learn how to integrate these skills with an application you build

# SQL Lesson: Exploring a Dataset

This lesson walks students through how to explore a simplified database using basic to intermediate SQL techniques, including `SELECT`, `DISTINCT`, `ORDER BY`, `WHERE`, `JOIN`, `COUNT`, `GROUP BY`, `AS` and more.


---
## What to Type in your terminal to get started 

Hit enter after each one. 

1. `sqlite3 JTCsql.db` 
2. `.tables` 
3. `.headers on`  
4. `.mode box` 

Helpful tip: If you get stuck in a shell/ your commands aren't working : hit `CONTROL + C` 2 times and then reset by typing `sqlite3 JTCsql.db` 

`sqlite3` is the Relational Database Management System, or RDBMS we are using and `JTCsql.db` is the database we are querying. Other RDBMSs include:

SQLite (what you’re using in this class — simple and fast) 
PostgreSQL (open-source, great for web apps)
MySQL (commonly used in websites) 
SQL Server (used in enterprise and business settings) 
Oracle DB (used in large corporations) 

## Tables Overview

| Table Name       | Description                                      |
|------------------|--------------------------------------------------|
| `demographics`   | One row per person in the dataset                |
| `legalall`       | One row per legal event — a person may have multiple|
| `programming`    | One row per person referred to programming       |
| `programmingLU`  | One row per program, with details                |



## JTCsql.db Tables

Mock database of a court based program data: 

```sql
SELECT * FROM demographics;     -- 1 row for every person in the dataset
SELECT * FROM legalall;         -- 1 row for every legal event (a person may have many)
SELECT * FROM programming;      -- 1 row per person who is referred to programming
SELECT * FROM programmingLU;    -- 1 row per program
```

## Viewing Tables

```sql
-- Format for viewing any table:
SELECT * FROM tablename;
```
Some basic notes on writing a query: 

1. `*` means ALL
2. Each query must be followed with a ;
3. Commands are generally not case sensitive, but it's good to get into this habit because it makes long code much more readable. 


## Exploring the `demographics` Table

```sql
SELECT * FROM demographics;
```

### SELECT vs SELECT DISTINCT

**In what situations may we want to see everything in a column vs. every unique thing in a column?**

```sql
SELECT zipcode FROM demographics;            -- lists every zipcode 
SELECT DISTINCT zipcode FROM demographics;   -- lists every unique zipcode
```
STUDENTS: if someone is asking what zipcodes are people in this database from, would we want to use `SELECT` or `SELECT DISTINCT` to get that information?

✅


STUDENTS: let's say your boss asks you "what languages do the people that come to our office speak?". How would you query that information from the demographics table?

✅

STUDENTS: a researcher wants a list of everyone in our demographics table, would we use `SELECT` or `SELECT DISTINCT`?

✅

In some situations, we do want potential repeats. For example, imagine if 2 people have the same name. These individuals should both still be accounted for. 


**How can we select more than one thing from a table?**

```sql
SELECT DISTINCT language, zipcode FROM demographics;
```

STUDENTS: try selecting the unique combination of race and gender from the demographics table

✅


## Sorting with ORDER BY

**Ordering data can help us interpret it more clearly**

SYNTAX:
```sql
SELECT desiredcolumn(s) FROM tablename and ORDER BY desiredcolumn
```
Let's say we want to view the different combinations of language and zipcodes in the demographcis table. We would write this query:

QUERY 1: 
```sql
SELECT DISTINCT language, zipcode FROM demographics;
```

From this default ordered output, it would be easy to make a list of each langage and the zip codes in which it's spoken:

English: 62701, 62702, 62703, 62704
Hindi: 62703
....

But what if I want to make a list of every langauge spoken in a zipcode instead, like:
62701: English, Hindi
62702: Spanish, ....

We need to order the output differently to make it more easy to read. 

QUERY 2: 
```sql
SELECT DISTINCT language, zipcode FROM demographics
ORDER BY zipcode;
```

Notice how ordering the data by the zip code makes it easier to read for this purpose. 

Let's explore more of our database.

```sql
SELECT * FROM demographics;                  -- 1 row per person
SELECT * FROM legalall;                      -- 1 row per case
```
The demographics table contains information about each person in the database. 

The legalall table contains information about each legal event in the database, so there could be more than 1 row per person. 

The organization of this table happens to be by person, but often times the rows of tables are not organized in any particular way. 

Let's say you are reviewing legal events by charge and you want to view this table more clearly since all of the charges are currently mixed together. 

STUDENTS: looking at the legalall table, which column should we order by?

✅

STUDENTS: Now, we not only want to see all of the events with a certain charge grouped together, but we also want to see the most recent ones first. What other column do we need to order by? Try to write that query.

Hint: 
the format for ordering by more than 1 column is:
```sql 
SELECT * FROM tablename ORDER BY column1, column2;
```

✅

Let's go back to the demographics table. 

```sql 
SELECT * FROM demographics;
```

Assume you are a researcher who has come across this table and who is interested in how many people in different age groups have encounters with the criminal justice system. What column might you ORDER BY to read this table more clearly?

✅

SQL defualts order by least to greatest, but we can also use `DESC` and `ASC` to specify if we want to see things in ascending or descending order. 

```sql
SELECT * FROM demographics ORDER BY age DESC;
SELECT * FROM demographics ORDER BY age ASC; -- default 
```

STUDENTS: how can we sort this table by the person's name alphabetically? 

✅

---

## WHERE, IN, and Wildcard Searches + a game! 
Sometimes, we do not need to see all of the data in a table. We can refine it using `WHERE`. 


I am specifically interested in looking for Asian people in our database. While I could use `ORDER BY` race and find these people, `WHERE` allows me to only view the people I am interested in. 
```sql
SELECT * FROM demographics WHERE race = 'Asian';
```

STUDENTS: let's say I am interested in viewing this same list of Asian people, but I want it to be in the order of oldest to youngest

HINT: `ORDER BY` comes after `WHERE` 

✅

Another example: I want to view anyone in the dataset who is 40 years old. 
```sql
SELECT * FROM demographics WHERE age = 40;
```

`WHERE` statements can also be used to identify a range for numbers and dates: 

```sql
SELECT * FROM demographics WHERE age < 40 AND age > 20; --between 20 and 40
SELECT * FROM legalall WHERE courtdate <= '2024-03-13'; -- on or before this date
```

We can also refine to groups of items using `WHERE` + `IN` or multiple = statements
```sql
SELECT * FROM demographics WHERE zipcode IN (62703, 62701);
SELECT * FROM demographics WHERE zipcode = 62703 OR zipcode = 62701;
```
Notice how these 2 statements do the same thing. 

STUDENTS: please use `WHERE` + `IN` to refine the data in demographics to include only Gary the Snail and SpongeBob SquarePants' records. 

✅

`WHERE` statements can also contain more complex logic:

Situation: A program has been created to provide resources to any individual that lives in the 62703 area OR any individual who lives in the 62701 area that is under 30 years old. They would like the names of those individuals: 

```sql
SELECT name, zipcode, age FROM demographics
WHERE zipcode = 62703 OR (zipcode = 62701 AND age < 30);
```

STUDENTS: Another program has been created and the following people are eligible: 
- Everyone in 62703 if they speak Spanish 
- Everyone in 62701 if they speak English 

Please provide the names, zipcodes, and languages of these individuals: 

Hint: Use () like this: (criteria one) OR (criteria two)

✅

## WILDCARD SEARCHING 

Wildcard searches using `LIKE` allow you to refine based on a partial match:

```sql
SELECT * FROM demographics WHERE name LIKE 'Ma%';
SELECT * FROM demographics WHERE name = 'Ma%';         -- This won’t work
SELECT * FROM demographics WHERE name LIKE '%Ma%';
SELECT * FROM demographics WHERE name LIKE '%Simpson';
```

STUDENTS: I'm looking for someone in the databse whose last name is Smith, but I can't remember their first name. How can I retrieve that?

✅

## COUNT + GROUP BY 

Simple counts: 

```sql
SELECT COUNT(DISTINCT personid) FROM demographics;
SELECT COUNT(DISTINCT personid) FROM legalall;
```

STUDENTS: write a query using `COUNT` to figure out how many programs are in the programmingLU table:

For referencing column names: 
```sql
SELECT * FROM programmingLU;
```

✅

`COUNT` can be paired with GROUP BY to aggregate 

See below how we would count the number of people per race in the demographics table:
```sql
SELECT race, COUNT(*) AS num_people --num_people is an alias to name the column in the output
FROM demographics
GROUP BY race;
```

STUDENTS: using the format of the query above, write a query that counts the number of people per zip code in the demographics table

✅

What if we want to count the distinct things in a group?
```sql 
SELECT zipcode, COUNT(DISTINCT language) AS num_languages
FROM demographics
GROUP BY zipcode;
```

## 🎮 Student Practice Game 

STUDENTS: Please complete the question and send me the name of the person in a private chat 

1. Please create a list of all males over the age of 18 in the demographics table in reverse alphabetical order (Z --> A). What is the 4th name in the list? 

✅

2. Find the name of the person who has an 's' in their name, speaks English, is over 60, and lives in the 62704 zip code...

✅

3. Please find the NAME of the person who had a Fraud legal event on Feb. 19, 2023. 

Hint: you will need to query the legalall table and then using information from that, you'll query the demographcis table...

✅


## 🔗 JOINs

Notice how in that last problem, we are referencing 2 tables. We can do that more efficiently using a `JOIN`. 

```sql 
SELECT * FROM legalall l
JOIN demographics d ON l.personid = d.personid 
WHERE courtdate = '2023-02-19' and topcharge = 'Fraud';
```
This statement combines the information from these 2 tables when we link them based on their column in common. 

Let's break down this proces with a more simple example: 

Our programming table contains a record for every referral to a program. 
```sql
SELECT * FROM programming;
```

Our programming look-up table contains a record for each possible program one can be referred to.
```sql
SELECT * FROM programmingLU;
```

The table I want to create has the following columns, which include information from both of these tables: 

personid, program name, chargeseligible, referral date

JOIN FORMULA 
```sql 
SELECT * (or columns)
FROM A 
JOIN B ON A.id = B.id;
```

STUDENTS: what column do both of these tables have in common? 

✅ 

```sql 
SELECT personid, name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid;

--notice how with a normal JOIN, it doesn't matter which table is the base table 

SELECT personid, name, chargeseligible, referraldate 
FROM programmingLU p
JOIN programming lu on p.programluid = lu.programluid;
```

Now, instead of the personid, we want the person's name. 

STUDENTS: What table table will we need to join to that has this new information?

✅ 

STUDENTS: What is a column that this table shares with either the programmingLU table or the programming table?

✅ 


STUDENTS: add another JOIN to this statement

```sql 
SELECT personid, name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid;
```

✅

Now we want to replace personid with the name from the demographics table. You'll notice that there is a name column in both the demographics table and the programming table. When that happens, we need to specify which tables we are geeting those columns from using the table alias:

```sql 
SELECT d.name, lu.name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```
To further clarify this output, we can also rename the coulmns using `AS` 

```sql 
SELECT d.name as [person name], lu.name as [program name], chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```

STUDENTS: please create a table that looks like this using JOINS 

person name, program name, completion date

✅


You'll notice that some of the program completion dates are NULL, as not everyone has completed their project. What if we only want the output for those with a completion date? 

We can add a where statement: 

```sql 
SELECT d.name AS [person name], lu.name AS [program name], completiondate 
FROM programming p
JOIN programmingLU lu ON p.programluid = lu.programluid
JOIN demographics d ON d.personid = p.personid
WHERE completiondate IS NOT NULL;
```

Last 2 questions for class: 

STUDENTS: You want to know how many legal events each person in the database has had. What SQL statement would you write using COUNT and GROUP BY?

✅

STUDENTS: What would we do if we want this to be a count with people's names instead of their personids? 

Hint for the GROUP BY: if 2 tables have the same column, you need to specify which table you're getting it from using the alias (ex. if both table A and table B have a referraldate column, you need to specifiy that you want a.referraldate or b.referraldate)

✅


RECAP: In this class you've learned how a relational database works and why we use them and how to retrieve information from a database using tools like `SELECT`, `DISTINCT`, `ORDER BY`, `WHERE`, `JOIN`, `COUNT`, `GROUP BY`, `AS`.

In our next class, we will go over your homework questions, do a short recap and then launch into our next subjects, including: 
1. Advanced aggregating/ counting 
2. Complex JOINS 
3. Creating your own database 

--- 
Homework:

1. Why is it ideal to use multiple tables instead of one giant table in a relational database?
2. Write a SQL query that counts the number of people in the demographics table for each zipcode who are under 30. 
3. You want to know how many people were referred to each program in the database. Referral information is stored in the programming table.Program names are stored in the programmingLU table. Write a SQL query that shows the program name and the number of people referred to each one.

# Welcome back to SQL Lesson #2

In the next 2 courses, we will learn more about joins, using HAVING, creating your own database/tables, and then we will end with an analytical project. 

Intro Question: What's been your favorite SQL skill or concept so far — or the one that “clicked”?

**Let's begin with a reivew:**

SETUP: 

Start by typing the following into your terminal:
1. sqlite3 JTCsql.db     (command for program + database instructions)
2. .mode box             (for formatting output)
3. .headers on           (for formatting output)

STUDENTS: What is a primary key vs. a foreign key? 

✅ 


STUDENTS: What's the difference between `SELECT` and `SELECT DISTINCT`?

✅ 

STUDENTS: How does `ORDER BY` help us interpret results? 

✅ 

STUDENTS:  Match the SQL keyword to its use case by pairing numbers with letters (e.g., 1 → B):**


| # | SQL Keyword |
|---|-------------|
| 1 | `WHERE`      |
| 2 | `IN`          |
| 3 | `LIKE`        |


| Letter | Use Case                                      |
|--------|-----------------------------------------------|
| A      | General filtering for one value or condition  |
| B      | Pattern matching for partial text values      |
| C      | Filtering for multiple specific values        |

✅  

Code Completion Practice

1. STUDENTS: Complete the code below to return all of the langauges spoken in the dataset 
```sql
SELECT DISTINCT ________ FROM demographics;
``` 

✅  


2. STUDENTS: Complete the code to return the number of females over 25 in the demographics table
``` sql 
SELECT COUNT(*) FROM demographics WHERE age > 25 AND ...
```

✅  


3. STUDENTS: Finish the code so that it returns the number of people in each zipcode
```sql
SELECT zipcode, COUNT(*) AS num_people FROM demographics ...
```

✅  

4. STUDENTS: finish the code to return all legal events with a Fraud charge, with the most recent one first: 
```sql
SELECT * FROM legalall WHERE topcharge = 'Fraud' ...
```

✅

# JOINS

Let's say we have 2 tables in a dataset:

`people`
| personid | name  |
|----------|-------|
| 1        | Alice |
| 2        | Bob   |
| 3        | Carla |

`pets`
| petid | personid | pet_name |
|-------|---------|----------|
| 101   | 1       | Fluffy   |
| 102   | 2       | Rex      |
| 103   | 2       | Daisy    |


As we discussed in the last class, information in a relational database is split into smaller sections, but let's say you'd like to make a table with the following information: 

Owner, Pet Name. These 2 columns live in 2 different tables. 

A `JOIN` allows you to combine information from 2 tables. 

```sql
SELECT p.name, pe.pet_name
FROM people p
JOIN pets pe ON p.personid = pe.personid;
```

| name  | pet_name |
| ----- | --------- |
| Alice | Fluffy    |
| Bob   | Rex       |
| Bob   | Daisy     |

Notice:
Carla from the people table doesn't appear in the results. That’s because the type of JOIN we’re using (also called an INNER JOIN) only shows rows where there’s a match in both tables.

In this case, only people who have a matching personid in the pets table will show up. Since Carla doesn’t have any pets listed, there's no matching row for her in the pets table — so she's excluded from the result.

If we still want Carla to appear, we use a `LEFT JOIN`. 

```sql
SELECT people.name
FROM people
LEFT JOIN pets ON people.personid = pets.ownerid;
```

Now we can still get information from both tables, but the table in the left join CAN have a blank field: 

| name  | pet_name |
| ----- | --------- |
| Alice | Fluffy    |
| Bob   | Rex       |
| Bob   | Daisy     |
| Carla   | NULL    |


STUDENTS: Write a query to list each pet and their owner's name, but only show pets whose owner’s name starts with the letter "A".
<details>
<summary>need a hint?</summary>
You will need to use LIKE to complete this question. 
</details>

✅


STUDENTS: Write a query to list all people who don’t have any pets 
<details>
<summary>need a hint?</summary>
No pets = only those whose pet_name is NULL. 

You will need to use LEFT JOIN and WHERE to complete this question. 
</details>

✅

STUDENTS: Count how many pets each person owns (include people with 0 pets).
<details>
<summary>need a hint?</summary>
You will need to use COUNT and GROUP BY to complete this question
</details>

✅

Let's apply these skills to `JTCsql.db`

The demographics table has 1 row per person. The programming table has 1 row per person who was referred to programming. 

STUDENTS: what is the difference in output between these 2 queries?

```sql
SELECT * FROM demographics d
JOIN programming p on d.personid = p.personid;
```

```sql
SELECT * FROM demographics d
LEFT JOIN programming p on d.personid = p.personid;
```

✅ 

STUDENTS: 
I would like to make a table with a person's name (demographics table) and the program referral date (programming). 
If I only want people who were referred to programming, how would I write this query?

✅

STUDENTS: I would like to have the same information as the above query, but I would like to add the program name from the `programmingLU` table. Please revise the query. 
<details>
<summary>need a hint?</summary>
to check what column links the programmingLU table and the program table, SELECT * FROM each of the tables and look at the column names. 
</details>

✅

Note: In this teaching database, the data is clean and well-structured, so `JOIN` often works just fine and as intended. But in real-world datasets — which are often messy or incomplete — we sometimes use a `LEFT JOIN` to avoid accidentally excluding rows.

For example, even though every programluid in the programming table should match a program in programmingLU, a `LEFT JOIN` ensures we still include people even if their program data is missing or incomplete.

```sql
SELECT d.name, lu.name, referraldate
FROM demographics d
JOIN programming p on d.personid = p.personid
LEFT JOIN programmingLU lu on lu.programluid = p.programluid;
```

One last practice problem before we move on to the next section: 

STUDENTS: Please query the name, court date, and topcharge of any individual with a Theft legal event. 

✅

# HAVING

This query returns programs and a count of the number of referrals per program (this was a practice question for you all at the end of the last class)

```sql
SELECT lu.name AS program_name, COUNT(*) AS num_referrals
FROM programming p
JOIN programmingLU lu ON p.programluid = lu.programluid
GROUP BY lu.name;
```
`HAVING` is used to filter groups after you've used `GROUP BY`. Think of it as `WHERE` but for grouped results. 

Let's say we want to refine this list to only include programs with over 5 people referred. This is when we would use HAVING 

```sql
SELECT lu.name AS program_name, COUNT(*) AS num_referrals
FROM programming p
JOIN programmingLU lu ON p.programluid = lu.programluid
GROUP BY lu.name
HAVING COUNT(*) > 5;
```

STUDENTS: Write a query that shows a count of people per zip code in the demographics table but only for zipcodes that have more than 10 people in it.

✅

STUDENTS: Find the charges (from legalall) that appear more than 2 times in the data.

✅


# CRUD OPERATIONS 

Exit the current shell by doing control + C

Begin by making a LOCAL copy of a new database with your name: by typing: `sqlite3 dylan.db` into the terminal 

Now, type in the same formatting comands we were using: 
1. .mode box 
2. .headers on

** Important note: you will not be pushing any of the following changes

`rm dylan.db` allows you to remove a local database (command this outside of the sqlite3 shell) 

## Section 1: `CREATE`

Each column of a table is assigned a data type. It's important to pick the correct data type because that allows certain functions to work vs. not. For example, if you make an interger a varchar value, you won't be able to count. 


| Data Type      | Description                                              | Example                          |
|----------------|----------------------------------------------------------|----------------------------------|
| `INTEGER`      | Whole numbers (used for IDs, counts, etc.)               | `42`, `0`, `-3`                  |
| `TEXT`         | Any string value  (replaced w/VARCHAR in SQLServer)                                       | `'Pizza'`, `'New York'`          |
| `VARCHAR(n)`   | A string with a max length of `n` (acts like `TEXT`)     | `'Tacos'`, `'Pad Thai'`          |
| `REAL`         | Floating-point numbers (decimals)                        | `8.5`, `3.14`                    |
| `DECIMAL(p,s)` | Precise decimal with `p` digits total, `s` after point   | `DECIMAL(6,2)` → `9999.99`       |
| `BOOLEAN`      | Represents true/false (stored as 1 or 0 in SQLite)       | `1` = true, `0` = false          |
| `DATE`         | Stores a date as a string in `YYYY-MM-DD` format         | `'2025-06-01'`                   |


```sql 
CREATE TABLE food(
	foodid INTEGER PRIMARY KEY AUTOINCREMENT,  
	foodname VARCHAR(50),      
   category VARCHAR(30),
   rating INT
);
```


| Column Name | Data Type                    | Explanation                                                                 |
|-------------|------------------------------|-----------------------------------------------------------------------------|
| `foodid`    | `INTEGER PRIMARY KEY AUTOINCREMENT` | A unique number assigned to each food item, automatically increases by 1 |
| `foodname`  | `VARCHAR(50)`                | A text field that can hold up to 50 characters (e.g., `'Tacos'`, `'Sushi'`) |
| `category`  | `VARCHAR(30)`                | A shorter text field for the type of cuisine (e.g., `'Mexican'`, `'Thai'`) |
| `rating`    | `INT`  (same as `INTEGER`)                      | A whole number rating from 1–10 or similar scale                            |


```sql
INSERT INTO food (foodname, category, rating)
VALUES 
  ('Sushi', 'Japanese', 9),
  ('Tacos', 'Mexican', 8),
  ('Pad Thai', 'Thai', 6),
  ('Deep Dish Pizza', 'Italian', 10),
  ('Hot Dog', 'American', 7);
```

## Section 2: READ

```sql 
SELECT * FROM food;
```

## PART 3: UPDATE
modify data in your dataset 

I went back to the Mexican place and decided the tacos were actually so good that I want to rate them 10

``` sql
UPDATE food 
SET rating = 10 
WHERE foodname = 'Tacos';
```

check the update 

```sql 
SELECT * FROM food;
```

STUDENTS: Try to rename the Deep Dish Pizza to Thin Crust Pizza 

✅

STUDENTS: Try to fix the rating of tacos to be 8.

✅

## Part 4: Delete
Remove data (carefully) from the dataset 

STUDENTS: Why do you think it is important to use a WHERE clause when running an UPDATE or DELETE statement? What would happen if you don’t?

✅

```sql 
DELETE FROM tablename 
WHERE column = ' '
```

STUDENTS: Last night you ate sushi and got food poisoning so you want to delete your sushi entry from the database [dont actually run this]. 

✅

STUDENTS: We only want top notch food in our data now. Please remove any food with a rating under 8 [don't actually run this]. 

✅

Real-world databases typically have multiple related tables. Let's look at how tables can be related. 

```sql 
-- Create a restaurants table
CREATE TABLE restaurants (
    restaurant_id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    cuisine_type TEXT,
    location TEXT,
    rating INTEGER
);
```

```sql 
INSERT INTO restaurants (name, cuisine_type, location, rating)
VALUES
('Joe''s Pizza', 'Italian', 'New York, NY', 7),
('Sushi Go', 'Japanese', 'San Francisco, CA', 2),
('La Taqueria', 'Mexican', 'Chicago, IL', 9),
('Green Earth', 'Vegetarian', 'Austin, TX', 8);
```

In a relational database, we could create relationships between foods and restaurants:
-- Example: A menu_items table linking foods and restaurants

```sql
CREATE TABLE menu_items (
    item_id INTEGER PRIMARY KEY AUTOINCREMENT,
    food_id INTEGER REFERENCES foods(foodid),
    restaurant_id INTEGER REFERENCES restaurants(restaurant_id),
    price DECIMAL (6,2)
);

-- Now let’s create a third table that connects foods to restaurants.
-- This is called a “junction table” and allows relationships to be has between the foods and restaurants tables.
-- Each row will represent a menu item (a specific food served at a specific restaurant).

INSERT INTO menu_items (food_id, restaurant_id, price)
VALUES
  (4, 1, 12.99),  -- Thin Crust Pizza at Joe's Pizza
  (1, 2, 14.50),  -- Sushi at Sushi Go
  (2, 3, 9.75),   -- Tacos at La Taqueria
  (5, 4, 7.25);   -- Hot Dog at Green Earth
```

STUDENTS: using informtion from the `menu_items`, `food`, and `restaurants` tables, please make a query that returns the `food name`, `restaurant name`, and `price`. 

✅

STUDENTS: please write a query that gives us the food name and the restaurant name 

HINT: we will still need the menu_items table here because it links tables to one another 

✅

STUDENTS: Write a query to find all restaurants that serve more than one menu item. List the restaurant name and the number of items they serve.


STUDENTS TRY: 
Try creating your own table called drinks with the following columns:
1. drinkid (primary key, auto-incrementing)
2. drinkname (max 50 characters)
3. type (e.g., soda, juice, water — max 30 characters)
4. caffeine (integer — mg per serving)

✅

STUDENTS: Add at least 3 drinks to your new table. One of them should have 0 caffeine.

✅

STUDENTS: You find out one of your drinks actually has 35mg of caffeine. Update it.

✅

STUDENTS: You want to cut back on soda. Delete all drinks in the drinks table where the type is 'Soda'.
(And if you don't have a soda, eliminate another type)

✅

STUDENTS: Can you write a query to list all drinks with more than 30mg of caffeine, ordered from highest to lowest?
✅

`rm dylan.db` (into the nain terminal) allows you to remove a local database (command this outside of the sqlite3 shell)

# That's all! 
In this lesson we have covered JOINS, HAVING, and CRUD operations. Next class, we will be doing an analytical project where you'll apply everythign you've learned so far! 

Closing question: what did you learn and what are you still confused about? 

# Welcome to Class 3! 

Review Questions: 

1. What do we need to be sure to include in an `UPDATE` or `DELETE` statement when modifying our databases? 

✅

2. In what format are DATEs stored in SQL?

✅

JOIN WARM-UP: 

3. You'd like to view the number of people per charge for each program:  

Your table will have these columns: PROGRAM NAME / CHARGE / PERSON COUNT

✅

# Analytics Project 

**Scenario: Your office has launched multiple diversion program pilots. Leadership wants a summary of how these programs are performing and who they’re reaching. Your job is to query the database and create a short, clear report that answers the questions below.**

Instructions: 
- You will create an analytical report that follows the structure below. You must answer the questions under each heading and please also add one of your own takeaways. 
- Remeber when you produce an analysis, you usually won't be presenting to people with a technical background. Therefore, you'll need to translate your data findings into a format that is digestible like a paragraph. 
- You will start by writing a queries to pull numbers and then make a document with your findings. 

Logistics: 
- You can choose to create one report per person or per group — but everyone should collaborate on the queries and help each other debug. 
- TAs and I will be available in a separate breakout room and will drop into groups periodically to check in.
- We will still take both 10-minute breaks: One at 7:30 and one at 8:30 (EST). 
- At 8:40 (EST), or earlier if groups are finished, we will regroup to discuss. I’ll invite groups to share their findings for each section if they’re comfortable.

Use the `JTCsql.db` database to complete this report. As a reminder: 

SETUP:

1. sqlite3 JTCsql.db 
2. .headers on 
3. .mode box 

TABLES:

| Table Name       | Description                                      |
|------------------|--------------------------------------------------|
| `demographics`   | One row per person in the dataset                |
| `legalall`       | One row per legal event — a person may have multiple|
| `programming`    | One row per person referred to programming       |
| `programmingLU`  | One row per program, with details                |


### 1. 🧾 Program Overview

- What are the programs? 
- When did each of them start? 
- What charge must a person have to be eligible for each program? 
- How many people have been referred to each program? 
      
<details>
<summary>💡 Starter queries</summary>

```sql
SELECT * FROM programmingLU;

SELECT ____, COUNT(*) 
FROM programming 
GROUP BY ____;
```
</details>


<br>

ex. 
When I run the following query: 

```sql 
SELECT * FROM programminglu;
```

I can start to write the following takeaway: 
The programs the office is offering include Intensive Relief, Speedy Recovery, Theft Counseling, and ReEnter. Intensive Relief and Speedy Recovery are 2 distinct programs for those facing drug charges and both are offered by hope treatments... 

### 2. 👥 Participant Demographics
- What is the racial breakdown of participants in each program?
- What is the gender breakdown?
- What is the average age per program? -- Hint: use AVG() instead of COUNT() in the same format

<details> <summary>💡 Starter queries</summary>

```sql
SELECT programluid, AVG(age)
...
GROUP BY programluid;
```
</details>

### 3. ⚖️ Legal Background
- What is the range of the number of legal events participants in each program has (between x and y legal events)?
- How many people referred to any program had 5 legal events?
- Along with the charge a participant MUST have to be eligible for a program, what are other common charges people in these programs are facing? 

<details> <summary>💡 Starter queries</summary>

```sql
SELECT p.personid, lu.name, COUNT(l.legaleventid) AS num_events
...
GROUP BY lu.name, p.personid;

....
HAVING COUNT(l.legaleventid) = 5;


SELECT lu.name AS program_name, l.topcharge, COUNT(DISTINCT p.personid) AS person_count
FROM programming p
JOIN legalall l ON l.personid = p.personid
JOIN programmingLU lu ON lu.programluid = p.programluid
GROUP BY lu.name, l.topcharge; 
```
</details>


### 4. ✅ Program Outcomes
- What is the average outreach time 
    Hint: This is a new function! `SELECT AVG(JULIANDAY(date1) - JULIANDAY(date2)) AS avg_outreach_days`
- How many people referred to the program decided to enroll in the program? 
- How many people have completed each program?
     Hint for what to add in writeup: can we draw conclusions about why a person has or hasn't completed a program from this data? What does this say about the success of each of the programs?


<details> <summary>💡 Starter queries</summary>

```sql

SELECT ____ , COUNT(*) AS completions
...
WHERE completiondate IS NOT NULL
GROUP BY lu.name;

```
</details>



### 5.⭐️ Extra Analysis (Take from here or create your own)
- What can we tell about the population who chose not to complete the program? 
- Both Intensive Relief and Speedy Recovery target those with drug charges. What makes these programs different? Which is "better"?


# Discussion questions:

1. Who would like to present section 1?
2. Who would like to present section 2?
3. Who would like to present section 3?
4. Who would like to present section 4?
5. Who would like to present section 5?

6. Did anyone produce a unique takeaway or do one of the optional analysis questions? and if so would you like to present?

7. What findings would you want to make sure to emphasize to the executives/ what stood out?

8. What disclaimers would you want to make about what conclusions are possible to derive from this dataset? 

9. What other sources of data could enrich this analysis more? 

# SQL Class Conclusion 

If you are interested in continuing to learn SQL, some next steps would be exploring the following concepts: `CTEs`, `TEMP TABLES`, `CASE WHEN`, `CONCAT`, `PARTITION`, `SUBQUERIES` and more. 
<br>
Continuing to learn with SQL will allow you to discover more efficient ways to complete tasks, but even with a strong foundation in the SQL basics you've learned here, you'll be able to complete pretty thorough analyses. 

Online Resources: https://sqliteonline.com/, https://mode.com/sql-tutorial, https://www.datacamp.com/ 

Books (both avaliable and affordable on Amazon):

- SQL Queries for Mere Mortals: A hands on Guide to Data Manipulation in SQL 
       - Software Independent, meaning that what you learn here is transferrable to any RDMS (SQLite, SQL Server etc.)
- Database Design for Mere Mortals: A hands on Guide to Relational Database Design 
       - Highly reccomend for those with data engineering goals. Walk you through best practices on designing a database (like making sure every table has a primary key), how to navigate designing a database based on an organization's needs, and some basic SQL

Using ChatGPT: In my experience ChatGPT won't always give you the most efficient ways to answer a SQL question, but it is excellent for troubleshooting typos and generating practice problems. If you give it some context like tables/columns/audience, it can respond pretty well. 

Thank you students and thank you for your support Afsana and Jason! 
<br>
Students, If I can be of help to you in any way don't hesitate to reach out: dylancomerford1@gmail.com 

