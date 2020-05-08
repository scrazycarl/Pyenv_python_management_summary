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
```SQL
SELECT ROUND (rental_rate/replacement_cost, 4)*100  > AS percent_cost
FROM FILM
```
