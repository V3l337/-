# TASK 1
```sql
SELECT DISTINCT district
FROM address
WHERE district LIKE 'K%a'
AND REPLACE(district, ' ', '') = district;  ## Проверяет, что в значении district нет пробелов. Функция REPLACE удаляет все пробелы, и если результат остается таким же, это означает, что пробелов не было 
```

![](screenshots/1.png)

# TASK 2
```sql
SELECT *
FROM payment p 
WHERE payment_date BETWEEN '2005-06-15 00:00:00' and '2005-06-18 23:59:59'
and amount > 10.00
```

![](screenshots/2.png)

# TASK 3
```sql
SELECT *
FROM rental r 
ORDER BY rental_date DESC 
limit 5
```

![](screenshots/3.png)

# TASK 4
```sql
SELECT 
  LOWER(REPLACE(first_name, 'll', 'pp')),
  LOWER(last_name)
FROM customer
WHERE active = 1
and first_name = 'kelly' OR first_name = 'whille'
```

![](screenshots/4.png)

# TASK 5
```sql
SELECT 
	SUBSTRING_INDEX(email, '@', 1),
	SUBSTRING_INDEX(email, '@', -1)
FROM customer c
```

# TASK 6
```sql
SELECT 
	CONCAT(UPPER(SUBSTRING(SUBSTRING_INDEX(email, '@', 1), 1, 1)), LOWER(SUBSTRING(SUBSTRING_INDEX(email, '@', 1), 2))),
	CONCAT(UPPER(SUBSTRING(SUBSTRING_INDEX(email, '@', -1), 1, 1)), LOWER(SUBSTRING(SUBSTRING_INDEX(email, '@', -1), 2)))
FROM customer
```


![](screenshots/5.png)


