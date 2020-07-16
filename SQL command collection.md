### Order

```sql
SELECT first_name, last_name FROM customer
ORDER BY store_id DESC, first_name ASC
LIMIT 5;
```

### LIMIT

```sql
SELECT title, length FROM film
ORDER BY length ASC
WHERE length<=50
LIMIT 10
```

### Between

1. Between is value <= and =>, it's inclusive. 
2. Not Between is a value < min, but >max .
3. It works with certain numbers but also dates.

```sql
SELECT * FROM payment
WHERE amount NOT BETWEEN 5 AND 9;
```
```sql
SELECT COUNT (*) FROM payment
WHERE payment_date NOT BETWEEN '2007-02-15' AND '2007-02-20';
```
### IN 

```sql
SELECT * FROM payment
WHERE amount IN (0.99,1.95,1.8)
ORDER BY amount
```

### Another way of matching: wildcared character "%", "_"

'%' represents more characters, '_' represents 1 character

e.g:

```sql
SELECT * FROM customer
WHERE first_name LIKE 'S%' AND last_name LIKE 'J%'
```

'LIKE' is case sensitive on matching characters. 'ILIKE' is case insensitive 

```sql
> SELECT * FROM customer
> WHERE first_name LIKE '%er%' 
```

## GROUP BY

Aggregate functions are: AVG(), COUNT(), MAX(), MIN(), SUM(), *ONLY used in SELECT or HAVING*
ROUND(function, decimalnumber) on top of the previous functions, will round up the result number

```sql
SELECT customer_id, staff_id, SUM(amount) FROM payment
GROUP BY customer_id, staff_id
ORDER BY staff_id,customer_id
```

```sql
SELECT customer_id, staff_id, SUM(amount) FROM payment
GROUP BY customer_id, staff_id
ORDER BY SUM(amount)    *The things that you used in "ORDER BY", needs to be used in SELECT before*
```

*Special notice: for date format, you need to transform it before you use it to order"

```sql
SELECT DATE(payment_date), SUM(amount) FROM payment
GROUP BY DATE(payment_date)
ORDER BY SUM(amount)
```

#### Filtering on Groupby 

```sql
SELECT customer_id, SUM(amount) FROM payment
GROUP BY customer_id
HAVING SUM(amount) > 100
```

```sql
SELECT customer_id,SUM (amount) FROM payment
WHERE staff_id = 2
GROUP BY customer_id
HAVING SUM(amount) >100
```


## JOIN

### As clause

As functions at the end of each query. So it doesn't work inside a "WHERE" clause. 
For example, the following command won't work

```sql
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
HAVING total_spent > 100   
```

*instead we should use SUM(amount) in the having clause*

### Inner join

```sql
SELECT * FROM Table_A
INNER JOIN Table_B
ON Table_B.col_to_match=Table_A.col_to_match
```

You can also specify the columns taken from each table as following. If the table you select only exits in one of the table.
Then no need to specify. 


```sql
SELECT staff.last_update, staff.username,staff.first_name FROM staff
INNER JOIN actor
ON actor.first_name=staff.first_name
```

#### Key points to high light:

1. The order of two tables to join doesn't matter
2. SQL treat "JOIN" as "INNER JOIN", but for good practice, always specify it's "INNER JOIN"
3. The two columns that have same information may have different column name in two tables. But a well designed data base should give them the same name.


### Outer join: allows two columns have different value 

This gives you full outer join: 

```sql
SELECT * FROM customer FULL OUTER JOIN payment
ON customer.customer_id=payment.customer_id 
```

*However, if there exists some columns doesn't have a mapping existed for some value, in other words, there are some rows that is unique to a table,
then it will be filled with null*

This following query is the exactly the opposite of inner join:

```sql
SELECT * FROM customer FULL OUTER JOIN payment
ON customer.customer_id=payment.customer_id 
WHERE customer.customer_id IS null
OR payment.payment_id IS null
```

#### Left  and Right outer join

The following queries means the film table is on the left, the inventory table be joined on to the film table.

```sql
SELECT film.film_id, film.title, inventory_id
FROM film
LEFT JOIN inventory ON
inventory.film_id=film.film_id
WHERE inventory.film_id IS NULL
```

And the exact reverse of Left outer join is right outer join, or switching the order of table in left outer join

```sql
SELECT film.film_id, film.title, inventory_id
FROM film
RIGHT OUTER JOIN inventory ON
inventory.film_id=film.film_id
WHERE inventory.film_id IS NULL
```

### Union

### Consecutive inner join 

The following two quires are identical:

```sql
SELECT first_name,last_name,title,film.film_id FROM actor
INNER JOIN film_actor
ON actor.actor_id=film_actor.actor_id
INNER JOIN film
ON film_actor.film_id=film.film_id
WHERE first_name='Nick' AND last_name='Wahlberg'
```

```sql
SELECT first_name, last_name,title, film.film_id FROM
(SELECT first_name,last_name,actor.actor_id,film_id FROM actor
INNER JOIN film_actor
ON actor.actor_id=film_actor.actor_id
WHERE first_name = 'Nick'and last_name='Wahlberg') as target_film
INNER JOIN film
ON target_film.film_id=film.film_id
```

## Advanced SQL query

Time information type. The difference matters when you want to design a database

```sql
TIME
DATE
TIMESTAMP
TIMESTAMPTZ
```

```sql
SELECT TIMEOFDAY()
SELECT NOW()
SELECT CURRENT_TIME
SELECT CURRENT_DATE
```

An example of TO_CHAR: 

```sql
SELECT TO_CHAR(payment_date,'MM/dd/YYYY')
FROM payment
```

*https://www.postgresql.org/docs/12/functions-formatting.html*

_Extract day information from date_:dow keyword

```sql
SELECT COUNT(payment_id) FROM payment
WHERE EXTRACT(dow FROM payment_date)=1
```

the above query, extracted payment which happened on Monday. So the SQL index start from Sunday as 0, Monday as 1.


### Math in SQL

It's just using normal math operations between columns.

```sql
SELECT ROUND (rental_rate/replacement_cost, 4)*100  > AS percent_cost
FROM FILM
```

### String Functions

There are normal string functions like other programming languages in SQL. 
Check it out here: [SQL String Funcitons]https://www.postgresql.org/docs/9.1/functions-string.html

```sql
SELECT first_name|| '-- ' ||last_name
FROM customer
```

```sql
SELECT LOWER(left(first_name,2)) || LOWER(right(last_name,3)) || '@gmail.com'
AS customer_email
FROM customer
```

### SUBQUERY

Basically 2 ways to do it: 1) Insert it with parenthesis 2) Use ```IN``` command for it

```sql
SELECT title, rental_rate
FROM film
WHERE rental_rate>(SELECT AVG(rental_rate) FROM film)
```
This is still okay when subqueries returns only 1 value, but if it returns multiple value, use ```IN```


Using ```EXIST` is a smart way to using information from another table, but avoid joining them.

```sql
SELECT first_name, last_name
FROM customer AS C
WHERE NOT EXISTS
(SELECT * FROM payment as P
WHERE p.customer_id=c.customer_id
AND amount>11)
```
But if you have need information from more than 1 table in the subquery, you need to use ```IN``` + ```JOIN```, for example

```sql
SELECT film_id,title
FROM film
WHERE film_id IN
(SELECT inventory.film_id
FROM rental
INNER JOIN inventory ON inventory.inventory_id=rental.inventory_id
WHERE return_date BETWEEN '2005-05-29' AND '2005-05-30')
ORDER BY title
```

### SELF JOIN

```sql
SELECT f1.title, f2.title, f1.length
FROM film AS f1
INNER JOIN film AS f2 ON
f1.film_id != f2.film_id
AND f1.length=f2.length
```


## Constraints

1. Column constraints 

```sql
1. Not Null
```

```sql
UNIQUE
```

```sql
CHECK
```

```sql
EXCLUSION
```

2. Table constraints

```sql
UNIQUE (column_list)
```

```sql
PRIMARY KEY (column_list)
```

## Table operations 

1. Create
```sql
CREATE TABLE table_name(
column_name TYPE column_constraint,
column_name TYPE column_constraint,
)
```

TYPE: 
*SERIAL* There are different length of serial that , 

```sql
CREATE TABLE account(
	user_id SERIAL PRIMARY KEY,
	username VARCHAR (50) UNIQUE NOT NULL,
	password VARCHAR(50) NOT NULL,
	email VARCHAR(30) UNIQUE NOT NULL,
	created_on TIMESTAMP NOT NULL,
	last_login TIMESTAMP
	)
```

```sql
CREATE TABLE account_job(
	user_id INTEGER REFERENCES account(user_id)
)
```

2. Insert

```sql
INSERT INTO table(column_1, column_2)
VALUES
(Value_1, Value_2)
```

3. Update

```sql
UPDATE TABLE
SET column1=value1, 
column2=value2
WHERE
condition
```

```sql
UPDATE account
SET last_login=CURRENT_TIMESTAMP
WHERE last_login=NULL
```

```sql
UPDATE TABLEA
SET original_col=TableB.new_col
FROM table_B
WHERE tableA_id=TableB_id
```
eg:

```sql
UPDATE account_job
SET hire_date=account.created_on
FROM account
WHERE account_job.user_id=account.user_id
```

You can also show all the rows affected by return

```sql
UPDATE Account
SET last_login=created_on,
RETURNING account_id, last_login
```

eg:

```sql
UPDATE account
SET last_login=CURRENT_TIMESTAMP
RETURNING email,created_on,last_login
```

4. DELETE

```sql
DELETE FROM employees
WHERE emp_id=2
```

```SQL
DELETE FROM TableA
USING TableB
WHERE TableA.id=TableB.id
```




4. Alter Table

The following command examples are alternative command available for the ```ALTER TABLE``` command

```sql
ALTER TABLE table_name
RENAME TO 
ALTER COLUMN col_name
    ADD COLUMN col_name TYPE
    DROP COLUMN col_name
    SET DEFAULT value
    DROP DEFAULT
    SET NOT NULL
    DROP NOT NULL
    ADD CONSTRAINT constraint_name
```

5. Check Constraints 

Setting constraints on conditions in relation to other column

eg:

```sql
CREATE TABLE employees(
emp_id SERIAL PRIMARY KEY,
first_name VARCHAR(50) NOT NULL,
last_name VARCHAR(50) NOT NULL,
birthday DATE CHECK (birthday > '1900-01-01'),
hire_date DATE CHECK (hire_date > birthday),
salary INTEGER CHECK (salary > 0)
)
```

## Conditional expression

Case, Coalesce, Cast, Nullif,Views, Import and Export

1. Case

```sql
SELECT a
CASE WHEN a = 1 THEN 'one',
     WHEN a = 2 THEN 'two'
ELSE 'other' AS label
END
FROM test;
```

CASE syntax (abov) VS Case Expression (Following)

So as shown above, **in this way, you don't need to know the existing value for a**

```sql
SELECT a 
    CASE a WHEN 1 THEN 'ONE'
        WHEN 2 THEN 'two'
        ELSE 'other'
    END
FROM test;
```

The unique value of 'case' keywords is to perform multiple action on same column and return result

eg:

```sql
SELECT 
SUM(CASE rating
	WHEN 'R' THEN 1
	ELSE 0
   END) AS r,
SUM(CASE rating
    WHEN 'PG' THEN 1
   ELSE 0
   END) AS pg,
SUM(CASE rating
   WHEN 'PG-13' THEN 1
   ELSE 0
   END) AS pg13
FROM film
```

2. Coalesce: use this to replace null values, without altering the original data

EG: in the following example, there is null values in ```DISCOUNT```, that's why COALESCE is applied there to substitue ```nul``` with ```0```

```SQL
SELECT item, (price - COALESCE(discount,0))
AS final FROM TABLE
```

2. CAST: convert datatype from one to another

```sql
SELECT CAST ('5' AS INTEGER)
```

```sql
SELECT '5' :: INTEGER
```

```sql
SELECT CAST (DATE AS TIMESTAMP)
FROM table
```

```sql
SELECT CHAR_LENGTH (CAST(inventory_id AS VARCHAR)) FROM rental
```

3.NULLIF: return null if the first value matches with second, otherwise, the first value
```sql
NULLIF(lastname, 0)
```

4. Views: a table that you always join together, then why not just save it as views

```sql
CREATE VIEW customer_info AS
SELECT customer.first_name, last_name, address FROM customer
INNER JOIN address
ON customer.address_id=address.address_id
```

```sql
CREATE OR REPLACE VIEW customer_info AS
```

```SQL
DROP VIEW IF EXISTS customer_info
```

```sql
ALTER VIEW customer_info
RENAME TO
```

### Import and Export

Importing CSV files doesn't automatically create a table. You can only copy it to an existing table.