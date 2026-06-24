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

### 📝 Задание 9

```sql
CREATE TABLE exam
(
	exam_id serial NOT NULL,
	exam_name varchar(64),
	exam_data date,
	CONSTRAINT exam_exam_id_key UNIQUE(exam_id)
);
```

### 📝 Задание 10

```sql
ALTER TABLE exam
DROP CONSTRAINT exam_exam_id_key;
```

### 📝 Задание 11

```sql
ALTER TABLE exam
ADD PRIMARY KEY (exam_id);
```

### 📝 Задание 12

```sql
CREATE TABLE person
(
	identity_id int PRIMARY KEY,
	last_name varchar(64),
	first_name varchar(64),
);
```

### 📝 Задание 13

```sql
CREATE TABLE passport
(
	passport_id int,
	serial_number int NOT NULL,
	registration varchar(256),
	identity_id int,
	CONSTRAINT pk_passport_pasport_id PRIMARY KEY (passport_id)
	CONSTRAINT fk_passport_indentity FOREIGN KEY (identity_id) REFERENCES person(identity_id)
);
```

### 📝 Задание 14

```sql
CREATE TABLE student
(
	student_id serial PRIMARY KEY,
	full_name varchar(256),
	curse int DEFAULT 1
);
```

### 📝 Задание 15

```sql
INSERT INTO student (full_name)
VALUES
	('Иван Иванов'),
	('Пётр Петров');

SELECT *
FROM student
```

### 📝 Задание 16

```sql
ALTER TABLE  student
ALTER COLUMN curse DROP DEFAULT;
```

### 📝 Задание 17

```sql
ALTER TABLE products
ADD CONSTRAINT chk_products_unit_price CHECK(unit_price > 0)
```

### 📝 Задание 18

```sql
CREATE SEQUENCE IF NOT EXISTS seq1
INCREMENT BY 1;
SELECT setval('seq1', (SELECT MAX(product_id) FROM products));

ALTER TABLE products
ALTER COLUMN product_id SET DEFAULT nextval('seq1');
```

### 📝 Задание 19

```sql
INSERT INTO products (product_name, supplier_id, category_id, quantity_per_unit, unit_price, units_in_stock, units_on_order, reorder_level, discontinued)
VALUES ('Крипто-Пряник', 1, 1, '10 шт в коробке', 15.50, 100, 0, 5, 0) RETURNING product_id;
```

</details>

<details>
<summary>📂 6. Проектирование БД</summary>
<br>
	
### В данной области я изучил следующие темы и ключевые моменты:

* **Стадии проектирования БД**
* **Анализ требований и логическое проектирование**
* **Моделирование БД (ER-диаграммы)**
* **Базовые рекомендации и плохие практики (Best Practices / Anti-patterns)**
* **Нормализация данных (изучены нормальные формы с 1НФ по 5НФ)**
  
</details>

<details>
<summary>📂 7. Представления (Views)</summary>
<br>
	
### 📝 Задание 1

```sql
CREATE VIEW the_first_view AS
SELECT 
	o.order_date,
	o.required_date,
	o.shipped_date,
	o.ship_postal_code,
	c.company_name,
	c.contact_name,
	c.phone,
	e.last_name,
	e.first_name,
	e.title
FROM orders o
JOIN customers c USING(customer_id)
JOIN employees e USING(employee_id)

SELECT *
FROM the_first_view
WHERE order_date > '1997-01-01';
```

### 📝 Задание 2

```sql
CREATE VIEW the_second_view AS
SELECT 
	o.order_date,
	o.required_date,
	o.shipped_date,
	o.ship_postal_code,
	o.ship_country,
	c.company_name,
	c.contact_name,
	c.phone,
	e.last_name,
	e.first_name,
	e.title
FROM orders o
JOIN customers c USING(customer_id)
JOIN employees e USING(employee_id)

--убедился, что нельзя добавлять столбцы

ALTER VIEW the_second_view
ADD COLUMN reports_to smallint;

ALTER VIEW the_second_view RENAME TO the_second_view_old;

CREATE VIEW the_second_view AS
SELECT 
	o.order_date,
	o.required_date,
	o.shipped_date,
	o.ship_postal_code,
	o.ship_country,
	c.company_name,
	c.contact_name,
	c.phone,
	e.last_name,
	e.first_name,
	e.title,
	e.postal_code,
	e.reports_to
FROM orders o
JOIN customers c USING(customer_id)
JOIN employees e USING(employee_id)

SELECT *
FROM the_second_view
ORDER BY ship_country;

DROP VIEW IF EXISTS the_second_view_old;
```

### 📝 Задание 3

```sql
CREATE OR REPLACE VIEW products_active AS
SELECT *
FROM products p 
WHERE discontinued = 0
WITH CHECK OPTION;

-- Проверка на защиту

INSERT INTO products_active (product_name, discontinued)
VALUES
	('Тест-Товар', 1);
```

</details>

<details>
<summary>📂 8. Логика CASE COALESCE и NULLIF</summary>
<br>

### 📝 Задание 1

```sql
INSERT INTO customers(customer_id, contact_name, city, country, company_name)
VALUES
	('AAAAA', 'Alfred Mann', NULL, 'USA', 'fake_company'),
	('BBBBB', 'Alfred Mann', NULL, 'Austria','fake_company');

SELECT c.contact_name, c.city, c.country
FROM customers c
ORDER BY 
    c.contact_name,
    CASE 
        WHEN c.city IS NULL THEN c.country 
        ELSE c.city                      
    END;

SELECT c.contact_name, c.city, c.country
FROM customers c
ORDER BY c.contact_name, COALESCE(c.city, c.country);
```

### 📝 Задание 2

```sql
SELECT p.product_name, p.unit_price,
	CASE 
		WHEN p.unit_price >= 100 THEN 'too expensive'
		WHEN p.unit_price >= 50 THEN 'average'
		ELSE 'low price'
	END AS price_category
FROM products p;
```

### 📝 Задание 3

```sql
SELECT c.contact_name, COALESCE(o.order_id::text, 'no orders') AS order_status
FROM customers c
LEFT JOIN orders o USING(customer_id)
WHERE o.order_id IS NULL;
```

### 📝 Задание 4

```sql
SELECT (e.last_name || ' ' || e.first_name) AS full_name, 
	CASE e.title
		WHEN 'Sales Representative' THEN 'Sales Stuff'
		ELSE e.title
	END AS job_title
FROM employees e;
```

</details>

<details>
<summary>📂 9. Функции SQL и PL pgSQL</summary>
<br>

### 📝 Задание 1
```sql
CREATE OR REPLACE FUNCTION cupy_customers()
RETURNS void AS $$ 
	DROP TABLE IF EXISTS customers_backup;
	SELECT *
	INTO customers_backup
	FROM customers;
$$ LANGUAGE sql;

SELECT cupy_customers();
```

### 📝 Задание 2
```sql
CREATE OR REPLACE FUNCTION avg_freight()
RETURNS real AS $$
	SELECT AVG(o.freight)
	FROM orders o;

$$ LANGUAGE sql;

SELECT avg_freight();
```

### 📝 Задание 3
```sql
CREATE OR REPLACE FUNCTION get_random_number(low_bound int, high_bound int)
RETURNS int AS $$
	SELECT floor(((high_bound + 1 - low_bound) * random()) + low_bound);
$$ LANGUAGE sql;

SELECT get_random_number(10, 20);
```

### 📝 Задание 4
```sql
ALTER TABLE employees
ADD COLUMN salary float8;

UPDATE employees
SET salary = 3000.00
WHERE city = 'London';

UPDATE employees
SET salary = 2500.00
WHERE city = 'Seattle';

INSERT INTO employees (employee_id, last_name, first_name, title, city, salary)
VALUES
	(10, 'Davolio', 'Andrew', 'Sales Representative', 'London', 1234.56),
	(11, 'Davolio', 'Andrew', 'Sales Representative', 'Seattle', 1234.56);

CREATE OR REPLACE FUNCTION get_min_and_max_salary(f_city character varying(15))
RETURNS TABLE(low_salary float8, high_salary float8) AS $$
	SELECT MIN(e.salary), MAX(e.salary)
	FROM employees e
	WHERE e.city = f_city;

$$ LANGUAGE sql;

SELECT * FROM get_min_and_max_salary('London');
```

### 📝 Задание 5
```sql
CREATE OR REPLACE FUNCTION upgrate_salary(percent float8 DEFAULT 0.15, upper_limit float8 DEFAULT 5000.00)
RETURNS void AS $$
	UPDATE employees
	SET salary = salary * (1 + percent)
	WHERE salary < upper_limit;

$$ LANGUAGE sql;

SELECT upgrate_salary();
```

### 📝 Задание 6
```sql
CREATE OR REPLACE FUNCTION upgrate_salary_get_info(percent float8 DEFAULT 0.15, upper_limit float8 DEFAULT 5000.00)
RETURNS SETOF employees AS $$
	UPDATE employees
	SET salary = salary * (1 + percent)
	WHERE salary < upper_limit
	RETURNING *;

$$ LANGUAGE sql;

SELECT * FROM upgrate_salary_get_info(0.2, 8000)
```

### 📝 Задание 7
```sql
CREATE OR REPLACE FUNCTION upgrate_salary_get_info_2(percent float8 DEFAULT 0.15, upper_limit float8 DEFAULT 5000.00)
RETURNS TABLE(out_last_name character varying(20), out_first_name character varying(10), out_title character varying(30), out_salary float8) AS $$
	UPDATE employees
	SET salary = salary * (1 + percent)
	WHERE salary < upper_limit
	RETURNING last_name AS out_last_name, first_name AS out_first_name, title AS out_title, salary AS out_salary;

$$ LANGUAGE sql;

SELECT * FROM upgrate_salary_get_info_2(0.2, 8000)
```

### 📝 Задание 8
```sql
CREATE OR REPLACE FUNCTION new_freight_avg(method_del smallint)
RETURNS SETOF orders AS $$
	DECLARE
    	max_freight numeric;
    	avg_freight numeric;
    	avg_total numeric;
	BEGIN
		SELECT MAX(freight) * 0.7, AVG(freight) 
		INTO max_freight, avg_freight 
		FROM orders 
		WHERE ship_via = method_del;
		
		avg_total := (max_freight + avg_freight) / 2;
		
		RETURN QUERY
		SELECT *
		FROM orders o
		WHERE o.freight < avg_total;
		
	END;
$$ LANGUAGE plpgsql;

-- или

CREATE OR REPLACE FUNCTION new_freight_avg(method_del smallint) 
RETURNS SETOF orders AS $$
    SELECT * 
    FROM orders 
    WHERE freight < (
        SELECT (MAX(freight) * 0.7 + AVG(freight)) / 2 
        FROM orders 
        WHERE ship_via = method_del
    );
$$ LANGUAGE sql;
```

### 📝 Задание 9
```sql
DROP FUNCTION salary_check;
CREATE OR REPLACE FUNCTION salary_check(salary numeric, max_salary numeric, min_salary numeric, percent float8)
RETURNS boolean AS $$

	BEGIN
		IF salary >= min_salary THEN 
			RETURN false;
		ELSEIF salary < min_salary THEN
			salary := salary * (1 + percent);
			IF salary > max_salary THEN
				RETURN false;
			ELSE
				RETURN true;
			 END IF;
		END IF;
	END;
$$ LANGUAGE plpgsql;
```

</details>

<details>
<summary>📂 10. Ошибки и их обработка</summary>
<br>
	
### 📝 Задание
```sql

CREATE OR REPLACE FUNCTION should_increase_salary(
    cur_salary numeric,
    max_salary numeric DEFAULT 80, 
    min_salary numeric DEFAULT 30,
    increase_rate numeric DEFAULT 0.2
    ) RETURNS bool AS $$
DECLARE
    new_salary numeric;
BEGIN
	IF min_salary > max_salary THEN
		RAISE EXCEPTION 'минимальный уровень з/п превышает максимальный' 
		USING ERRCODE=45000;
	ELSIF min_salary < 0 or max_salary < 0 THEN
		RAISE EXCEPTION 'ни минимальный, ни максимальный уровень з/п не могут быть меньше нуля' 
		USING ERRCODE=45001;
	ELSIF increase_rate < 0.05 THEN
		RAISE EXCEPTION 'коэффициент повышения зарплаты не может быть ниже 5%%'
		USING ERRCODE=45002;
	END IF;
	
    IF cur_salary >= max_salary OR cur_salary >= min_salary THEN        
        RETURN false;
    END IF;
    
    IF cur_salary < min_salary THEN
        new_salary = cur_salary + (cur_salary * increase_rate);
    END IF;
    
    IF new_salary > max_salary THEN
        RETURN false;
    ELSE
        RETURN true;
    END IF;    
END;
$$ LANGUAGE plpgsql;


CREATE OR REPLACE FUNCTION should_increase_salary_examination(
	cur_salary numeric,
    max_salary numeric DEFAULT 80, 
    min_salary numeric DEFAULT 30,
	increase_rate numeric DEFAULT 0.2
) RETURNS text AS $$
DECLARE
	err_ctx text;
	err_msg text;
	err_code text;
BEGIN
	RETURN should_increase_salary(cur_salary, max_salary, min_salary, increase_rate);
EXCEPTION 
	WHEN SQLSTATE '45000', SQLSTATE '45001', SQLSTATE '45002' THEN
		GET STACKED DIAGNOSTICS
			err_ctx = PG_EXCEPTION_CONTEXT,
			err_msg = MESSAGE_TEXT,
			err_code = RETURNED_SQLSTATE;
		RAISE INFO 'My custom handler: ';
		RAISE INFO 'Error msg: %', err_msg;
		RAISE INFO 'Error code: %', err_code;
		RAISE INFO 'Error ctx: %', err_ctx;
		RETURN NULL;
END;
$$ LANGUAGE plpgsql;
```
</details>

<details>
<summary>📂 11. Приведение типов данных</summary>
<br>

</details>

<details>
<summary>📂 12. Индексы</summary>
<br>

### 📝 Задание 1
```sql
CREATE INDEX idx_orders_employee_id ON orders(employee_id);

SELECT o.order_id, o.order_date, e.last_name 
FROM orders o
JOIN employees e ON o.employee_id = e.employee_id
WHERE o.employee_id = 4;
```

### 📝 Задание 2
```sql
CREATE INDEX idx_customers_company_name ON customers(company_name varchar_pattern_ops);

SELECT customer_id, company_name, contact_name 
FROM customers 
WHERE company_name LIKE 'M%';
```

### 📝 Задание 3
```sql
CREATE INDEX idx_orders_ship_country ON orders(ship_country, order_date NULLS LAST);

SELECT order_id, customer_id, order_date, ship_country 
FROM orders 
WHERE ship_country = 'France' 
ORDER BY order_date ASC;
```

### 📝 Задание 4
```sql
CREATE INDEX idx_orders_date ON orders(shipped_date, required_date)
WHERE shipped_date > required_date;

SELECT order_id, customer_id, shipped_date, required_date 
FROM orders 
WHERE shipped_date > required_date;
```

### 📝 Задание 5
```sql
CREATE INDEX idx_order_details_order_id ON order_details(order_id)
INCLUDE (product_id, unit_price);

SELECT product_id, unit_price 
FROM order_details 
WHERE order_id = 10250;
```

### 📝 Задание 6
```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;

CREATE INDEX idx_products_product_name ON products USING GIN(product_name gin_trgm_ops);

SELECT product_id, product_name 
FROM products 
WHERE product_name LIKE '%Toast%';
```

### 📝 Задание 7
```sql
CREATE TABLE system_logs (
    id SERIAL PRIMARY KEY,
    status TEXT,
    payload TEXT
);

INSERT INTO system_logs (status, payload)
SELECT 
    CASE WHEN random() > 0.1 THEN 'SUCCESS' ELSE 'ERROR' END,
    repeat('some_data_', 5)
FROM generate_series(1, 100000);

CREATE INDEX idx_logs_status ON system_logs(status);

EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM system_logs WHERE status = 'ERROR';

UPDATE system_logs SET status = 'ERROR' WHERE status = 'ERROR';
UPDATE system_logs SET status = 'ERROR' WHERE status = 'ERROR';
UPDATE system_logs SET status = 'ERROR' WHERE status = 'ERROR';
UPDATE system_logs SET status = 'ERROR' WHERE status = 'ERROR';
UPDATE system_logs SET status = 'ERROR' WHERE status = 'ERROR';

EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM system_logs WHERE status = 'ERROR';

VACUUM system_logs;
REINDEX INDEX idx_logs_status;
```

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
