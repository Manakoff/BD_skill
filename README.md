# 📚 Мое портфолио по базам данных (PostgreSQL)

Репозиторий содержит выполненные практические задания и домашние работы в рамках курса по изучению SQL и СУБД PostgreSQL.
Работал с бд Northwind

---

## 🛠️ Темы курса и выполненные задания

<details>
<summary>📂 1. Введение в SQL</summary>
<br>

</details>

<details>
<summary>📂 2. Простые запросы с SELECT</summary>
<br>

</details>

<details>
<summary>📂 3. Соединение - Joins</summary>
<br>

### 📝 Задание 1
```sql
SELECT c.company_name AS "Компания заказчика", (e.last_name || ' ' || e.first_name) AS "ФИО сотрудника"
FROM orders o
JOIN customers c USING (customer_id)
JOIN employees e USING (employee_id)
JOIN shippers s ON s.shipper_id = o.ship_via
WHERE c.city = 'London' 
	AND e.city = 'London'
	AND s.company_name = 'Speedy Express';
```

### 📝 Задание 2
```sql
SELECT p.product_name AS "Наименование продуктов", p.units_in_stock AS "Кол-во товаров",
	s.contact_name AS "ФИО", s.phone AS "Телефон"
FROM products p
JOIN categories c USING(category_id)
JOIN suppliers s USING(supplier_id)
WHERE p.units_in_stock < 20
	AND c.category_name IN ('Beverages', 'Seafood')
	AND	discontinued = 0
ORDER BY "Кол-во товаров" DESC;
```

### 📝 Задание 3
```sql
SELECT c.contact_name AS "Имя заказчика"
FROM customers c
LEFT JOIN orders o USING(customer_id)
WHERE o.order_id IS NULL;
```

### 📝 Задание 4
```sql
SELECT c.contact_name AS "Имя заказчика"
FROM orders o
RIGHT JOIN customers c USING(customer_id)
WHERE o.order_id IS NULL;
```
</details>

<details>
<summary>📂 4. Подзапросы</summary>
<br>

### 📝 Задание 1

```sql
SELECT p.product_name, p.units_in_stock
FROM products p
WHERE p.units_in_stock < (
	SELECT AVG(quantity) AS quantity
	FROM order_details od
	GROUP BY od.product_id
	ORDER BY quantity
	LIMIT 1
);
```

### 📝 Задание 2

```sql
SELECT o.customer_id, SUM(o.freight) AS freight_sum
FROM orders o
WHERE o.shipped_date BETWEEN '1996-07-16' AND '1996-07-31'
  AND o.freight >= (
      SELECT AVG(freight) 
      FROM orders
  )
GROUP BY o.customer_id
ORDER BY freight_sum;
```

### 📝 Задание 3

```sql
SELECT o.customer_id, o.ship_country, order_costs.order_price
FROM (
	SELECT od.order_id, SUM(od.unit_price * od.quantity * (1 - od.discount)) AS order_price 
	FROM order_details od
	GROUP BY od.order_id
	) AS order_costs 
JOIN orders o USING(order_id)
WHERE o.order_date >= '1997-09-01'
	AND o.ship_country IN ('Argentina', 'Brazil', 'Venezuela')
ORDER BY order_costs.order_price DESC
LIMIT 3;
```

### 📝 Задание 4

```sql
SELECT p.product_name
FROM products p
WHERE p.product_id IN (
    SELECT od.product_id
    FROM order_details od
    GROUP BY od.product_id
    HAVING SUM(od.quantity) = 10
);
```

</details>

<details>
<summary>📂 5. DDL - управляем БД</summary>
<br>
	
### 📝 Задание 1

```sql
CREATE TABLE teacher
(
	teacher_id serial PRIMARY KEY,
	first_name varchar,
	last_name varchar,
	birthday date,
	phone varchar,
	title varchar
);

```

### 📝 Задание 2

```sql
ALTER TABLE teacher 
ADD middle_name varchar;
```

### 📝 Задание 3

```sql
ALTER TABLE teacher 
DROP COLUMN middle_name;
```

### 📝 Задание 4

```sql
ALTER TABLE teacher
RENAME COLUMN birthday TO birth_date;
```

### 📝 Задание 5

```sql
ALTER TABLE teacher
ALTER COLUMN phone SET DATA TYPE varchar(32);
```

### 📝 Задание 6

```sql
CREATE TABLE exam (
	exam_id serial PRIMARY KEY,
	exam_name varchar(256),
	exam_date date
)
```

### 📝 Задание 7

```sql
INSERT INTO exam (exam_name, exam_date)
VALUES 
    ('Математика', '2026-06-01'),
    ('Физика', '2026-06-05'),
    ('Информатика', '2026-06-10');
```
### 📝 Задание 8

```sql
TRUNCATE TABLE exam RESTART IDENTITY;
```
</details>

<details>
<summary>📂 6. Проектирование БД</summary>
<br>

</details>

<details>
<summary>📂 7. Представления (Views)</summary>
<br>

</details>

<details>
<summary>📂 8. Логика CASE COALESCE и NULLIF</summary>
<br>

</details>

<details>
<summary>📂 9. Функции SQL и PL pgSQL</summary>
<br>

</details>

<details>
<summary>📂 10. Ошибки и их обработка</summary>
<br>

</details>

<details>
<summary>📂 11. Приведение типов данных</summary>
<br>

</details>

<details>
<summary>📂 12. Индексы</summary>
<br>

</details>

<details>
<summary>📂 13. Массивы</summary>
<br>

</details>

<details>
<summary>📂 14. Пользовательские типы</summary>
<br>

</details>

<details>
<summary>📂 15. Продвинутая группировка с CUBE и ...</summary>
<br>

</details>

<details>
<summary>📂 16. Знакомство с psql</summary>
<br>

</details>

<details>
<summary>📂 17. Импорт данных</summary>
<br>

</details>

<details>
<summary>📂 18. CTE - Common Table Expressions</summary>
<br>

</details>

<details>
<summary>📂 19. Оконные функции</summary>
<br>

</details>

<details>
<summary>📂 20. Транзакции</summary>
<br>

</details>

<details>
<summary>📂 21. Триггеры</summary>
<br>

</details>

<details>
<summary>📂 22. Безопасность в postgresql</summary>
<br>

</details>
