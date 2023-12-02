# Домашнее задание к занятию «SQL. Часть 2» - Костюк Денис

## Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

### Ответ 1

SELECT CONCAT(s2.first_name, ' ', s2.last_name) as ФИО, c3.city as Город, COUNT(c.customer_id) as "Кол-во клиентов"
FROM customer c
JOIN store s on c.store_id = s.store_id
JOIN staff s2 on s2.staff_id = s.manager_staff_id
JOIN address a on a.address_id = s.address_id
JOIN city c3 on c3.city_id = a.city_id
GROUP BY c.store_id
HAVING COUNT(c.customer_id) > 300;


## Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

### Ответ 2

SELECT COUNT(f.`length`) as "кол-во длинных фильмов" 
FROM film f 
WHERE  f.`length` > (SELECT SUM(f2.`length`) / COUNT(f2.`length`) from film f2);

## Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

### Ответ 3

SELECT MONTH (p.payment_date) as mesyac, SUM(p.amount) as summa, (
	SELECT COUNT(r.rental_id) 
	from rental r 
	WHERE MONTH(r.rental_date)=mesyac
) as prodaji
FROM payment p 
GROUP BY MONTH(payment_date)
ORDER BY SUM(p.amount) DESC
LIMIT 1;

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

## Задание 4*
Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

### Ответ 4

SELECT CONCAT(s.first_name, ' ', s.last_name) as "Продаван", COUNT(p.payment_id) as "Продажи",
	CASE
		WHEN COUNT(p.payment_id) > 8000 THEN 'Да'
		WHEN COUNT(p.payment_id) < 8000 THEN 'Нет'
	ELSE 'Average user'
	END AS "Премия"
FROM payment p 
RIGHT JOIN staff s on s.staff_id = p.staff_id 
GROUP BY s.staff_id;

## Задание 5*
Найдите фильмы, которые ни разу не брали в аренду.

### Ответ 5

SELECT *
FROM film f 
LEFT JOIN inventory i on i.film_id = f.film_id 
LEFT JOIN rental r on r.inventory_id = i.inventory_id 
WHERE r.rental_id is NULL;
