### Order
> `SELECT first_name, last_name FROM customer
> ORDER BY store_id DESC, first_name ASC
> LIMIT 5`
### LIMIT
> SELECT title, length FROM film
> ORDER BY length ASC
> WHERE length<=50
> LIMIT 10
>

### Between
1. Between is value <= and =>, it's inclusive. 
2. Not Between is a value < min, but >max .
3. It works with certain numbers but also dates.
> SELECT * FROM payment
> WHERE amount NOT BETWEEN 5 AND 9;

> SELECT COUNT (*) FROM payment
> WHERE payment_date NOT BETWEEN '2007-02-15' AND '2007-02-20';

### IN 
> SELECT * FROM payment
> WHERE amount IN (0.99,1.95,1.8)
> ORDER BY amount

### Another way of matching: wildcared character "%", "_"
'%' represents more characters, '_' represents 1 character

e.g:

> SELECT * FROM customer
> WHERE first_name LIKE 'S%' AND last_name LIKE 'J%'
'LIKE' is case sensitive on matching characters. 'ILIKE' is case insensitive 

> SELECT * FROM customer
> WHERE first_name LIKE '%er%' 

## GROUP BY
Aggregate functions are: AVG(), COUNT(), MAX(), MIN(), SUM(), *ONLY used in SELECT or HAVING*
ROUND(function, decimalnumber) on top of the previous functions, will round up the result number

> SELECT customer_id, staff_id, SUM(amount) FROM payment
> GROUP BY customer_id, staff_id
> ORDER BY staff_id,customer_id

> SELECT customer_id, staff_id, SUM(amount) FROM payment
> GROUP BY customer_id, staff_id
> ORDER BY SUM(amount)    *The things that you used in "ORDER BY", needs to be used in SELECT before*

*Special notice: for date format, you need to transform it before you use it to order"

> SELECT DATE(payment_date), SUM(amount) FROM payment
> GROUP BY DATE(payment_date)
> ORDER BY SUM(amount)

#### Filtering on Groupby 

> SELECT customer_id, SUM(amount) FROM payment
> GROUP BY customer_id
> HAVING SUM(amount) > 100

> SELECT customer_id,SUM (amount) FROM payment
> WHERE staff_id = 2
> GROUP BY customer_id
> HAVING SUM(amount) >100
