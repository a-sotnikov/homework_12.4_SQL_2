# Домашнее задание к занятию "SQL - часть 1" - `Андрей Сотников`

---

### Задание 1

> Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:
>
> - фамилия и имя сотрудника из этого магазина;
> - город нахождения магазина;
> - количество пользователей, закреплённых в этом магазине.

```sql
SELECT 
    qty.store_id, 
    CONCAT(staff.first_name, ' ', staff.last_name) AS stuff_name, 
    city.city AS store_city, 
    qty.customer_qty  
FROM (
    SELECT store_id, COUNT(store_id) AS customer_qty  
    FROM customer 
    GROUP BY store_id
    HAVING COUNT(store_id) > 300) AS qty
LEFT JOIN staff on staff.store_id = qty.store_id
LEFT JOIN store ON store.store_id = qty.store_id
LEFT JOIN address ON address.address_id = store.address_id
LEFT JOIN city ON city.city_id = address.city_id 
```

### Задание 2

> Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```sql
SELECT COUNT(film_id) FROM film
WHERE `length` > (SELECT AVG(`length`) FROM film)
```

### Задание 3

> Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

```sql
SELECT 
    MONTHNAME(payment_date) AS payment_month, 
    SUM(amount) AS total_amount, 
    COUNT(rental_id) AS rental_qty 
FROM payment
GROUP BY payment_month
ORDER BY total_amount DESC
LIMIT 1
```

### Задание 4*

> Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

```sql
SELECT staff_id, COUNT(rental_id),
    CASE 
        WHEN COUNT(rental_id) > 8000 THEN 'Да'
        ELSE 'Нет'
    END AS Премия
FROM rental
GROUP BY staff_id 
```

### Задание 5*

> Найдите фильмы, которые ни разу не брали в аренду.

```sql
SELECT f.title FROM film f
LEFT JOIN inventory AS i ON i.film_id = f.film_id
LEFT JOIN rental AS r ON r.inventory_id = i.inventory_id
GROUP BY f.title
HAVING COUNT(r.rental_date)=0
```
