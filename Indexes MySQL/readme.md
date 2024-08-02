# TASK 1

#### Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```sql
SELECT 
	SUM(data_length) / SUM(data_length + index_length) AS Procent
FROM 
	INFORMATION_SCHEMA.TABLES
WHERE 
	table_schema = "sakila"
```

# TASK 2

#### Выполните explain analyze следующего запроса: 1-перечислите узкие места; 2-оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

0. Вывод исходного запроса
```
   -> Table scan on <temporary>  (cost=2.5..2.5 rows=0) (actual time=18759..18759 rows=391 loops=1)
    -> Temporary table with deduplication  (cost=0..0 rows=0) (actual time=18759..18759 rows=391 loops=1)
        -> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title )   (actual time=8035..17927 rows=642000 loops=1)
            -> Sort: c.customer_id, f.title  (actual time=8035..8267 rows=642000 loops=1)
                -> Stream results  (cost=10.8e+6 rows=16.7e+6) (actual time=0.815..6336 rows=642000 loops=1)
                    -> Nested loop inner join  (cost=10.8e+6 rows=16.7e+6) (actual time=0.806..5380 rows=642000 loops=1)
                        -> Nested loop inner join  (cost=9.12e+6 rows=16.7e+6) (actual time=0.796..4944 rows=642000 loops=1)
                            -> Nested loop inner join  (cost=7.45e+6 rows=16.7e+6) (actual time=0.782..4357 rows=642000 loops=1)
                                -> Inner hash join (no condition)  (cost=1.65e+6 rows=16.5e+6) (actual time=0.76..136 rows=634000 loops=1)
                                    -> Filter: (cast(p.payment_date as date) = '2005-07-30')  (cost=1.72 rows=16500) (actual time=0.0652..12.1 rows=634 loops=1)
                                        -> Table scan on p  (cost=1.72 rows=16500) (actual time=0.0487..9.31 rows=16044 loops=1)
                                    -> Hash
                                        -> Covering index scan on f using idx_title  (cost=103 rows=1000) (actual time=0.0622..0.491 rows=1000 loops=1)
                                -> Covering index lookup on r using rental_date (rental_date=p.payment_date)  (cost=0.25 rows=1.01) (actual time=0.0046..0.00635 rows=1.01 loops=634000)
                            -> Single-row index lookup on c using PRIMARY (customer_id=r.customer_id)  (cost=250e-6 rows=1) (actual time=527e-6..563e-6 rows=1 loops=642000)
                        -> Single-row covering index lookup on i using PRIMARY (inventory_id=r.inventory_id)  (cost=250e-6 rows=1) (actual time=401e-6..437e-6 rows=1 loops=642000)
```
   

2. -Отсутствие индексов, замедляет выполнение запросов. Фильтрация по функции date тоже странная

3.
```sql
select 
	concat(c.last_name, ' ', c.first_name), 
	sum(p.amount) over (partition by c.customer_id, f.title)
from 
	payment p
join rental r ON p.rental_id = r.rental_id
join customer c ON r.customer_id = c.customer_id 
join inventory i ON c.store_id = i.store_id 
join film f ON i.film_id = f.film_id
where 
	p.payment_date >= "2005-07-30 00:00:00" AND 
	p.payment_date < "2005-07-31 00:00:00";
```

4. Вывод нового запроса
```
-> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title )   (actual time=8934..32140 rows=1.45e+6 loops=1)
    -> Sort: c.customer_id, f.title  (actual time=8934..9365 rows=1.45e+6 loops=1)
        -> Stream results  (cost=1.89e+6 rows=4.2e+6) (actual time=0.219..5860 rows=1.45e+6 loops=1)
            -> Nested loop inner join  (cost=1.89e+6 rows=4.2e+6) (actual time=0.209..4319 rows=1.45e+6 loops=1)
                -> Nested loop inner join  (cost=424067 rows=4.2e+6) (actual time=0.199..1186 rows=1.45e+6 loops=1)
                    -> Nested loop inner join  (cost=2957 rows=1833) (actual time=0.157..34.5 rows=634 loops=1)
                        -> Nested loop inner join  (cost=2316 rows=1833) (actual time=0.145..31.4 rows=634 loops=1)
                            -> Filter: ((p.payment_date between '2005-07-30 00:00:00' and '2005-07-30 23:59:59') and (p.rental_id is not null))  (cost=1674 rows=1833) (actual time=0.125..25.2 rows=634 loops=1)
                                -> Table scan on p  (cost=1674 rows=16500) (actual time=0.0775..10.8 rows=16044 loops=1)
                            -> Single-row index lookup on r using PRIMARY (rental_id=p.rental_id)  (cost=0.25 rows=1) (actual time=0.00919..0.00922 rows=1 loops=634)
                        -> Single-row index lookup on c using PRIMARY (customer_id=r.customer_id)  (cost=0.25 rows=1) (actual time=0.00454..0.00457 rows=1 loops=634)
                    -> Covering index lookup on i using idx_store_id_film_id (store_id=c.store_id)  (cost=0.794 rows=2290) (actual time=0.037..1.56 rows=2288 loops=634)
                -> Single-row index lookup on f using PRIMARY (film_id=i.film_id)  (cost=0.25 rows=1) (actual time=0.00183..0.00187 rows=1 loops=1.45e+6)
```

