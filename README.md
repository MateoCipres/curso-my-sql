# curso-my-sql
Consultas necesarias para aprender sobre mysql hecho personalmente



--CREATE

CREATE TABLE users (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR (100) NOT NULL,
	email VARCHAR (150) UNIQUE NOT NULL,
	password VARCHAR (255),
	created_at DATE DEFAULT (CURRENT_DATE)
);

CREATE TABLE products (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR (100) NOT NULL,
	description TEXT,
	price DECIMAL(10,2) CHECK (price > 0),
	stock INT DEFAULT 0,
	created_at DATE DEFAULT (CURRENT_DATE)
);

CREATE TABLE orders (
	id INT AUTO_INCREMENT PRIMARY KEY,
	user_id INT NOT NULL,
	product_id INT NOT NULL,
	quantity INT DEFAULT 1 CHECK (quantity > 0),
	total DECIMAL(10,2) CHECK (total > 0),
	order_date DATE DEFAULT (CURRENT_DATE),
	FOREIGN KEY (user_id) REFERENCES users(id),
	FOREIGN KEY (product_id) REFERENCES products(id)
);

SHOW TABLES;



DESCRIBE users;
DESCRIBE products;
DESCRIBE orders;

--INSERT

INSERT INTO users (name, email, password, cellphone)
VALUES 
('Sergie Code', 'info@sergiecode.com', 'ABCabc123,.-', '+44789456123'),
('Ricardo Darin', 'ricardo@darin.com', 'elsecretodetusojos', '+11978954132'),
('Jenna Ortega', 'wednesday@addams.com', 'merlinamiercoles', '+349188646486');

INSERT INTO users (name, email, password, cellphone)
VALUES 
('Leonardo Dicaprio', 'leo@dicaprop.com', '123456', '+12312541243'),
('rhea seehorn', 'pluribus@appletv.com', '123456', '+1231254242');


INSERT INTO products (name, description, price,stock)
VALUES
('Notebook', 'Sirve para programar', 1000.05, 1),
('Mouse', NULL, 5.50, 10),
('Keyboard', NULL, 5.50, NULL);

INSERT INTO products (name, description, price)
VALUES
('Micrófono', 'Calidad para Streaming', 305.10);

INSERT INTO orders (user_id, product_id, quantity, total)
VALUES
(1,1,1,1000.05),
(2,2,1,5.50),
(3,4,2,610.20);


SELECT * FROM users;
SELECT * FROM products;
SELECT * FROM orders;


SHOW tables;

-- ALTER

ALTER TABLE users ADD COLUMN phone VARCHAR(20);

ALTER TABLE users DROP COLUMN phone;

ALTER TABLE users RENAME COLUMN phone TO cellphone;

ALTER TABLE users DROP COLUMN cellphone;

ALTER TABLE users MODIFY COLUMN cellphone VARCHAR(30) NOT NULL;

ALTER TABLE users ADD CONSTRAINT cellphone UNIQUE (cellphone);

ALTER TABLE users RENAME TO	customers;

DESCRIBE customers;
DESCRIBE users;
DESCRIBE orders;
DESCRIBE products;

SHOW tables;

--UPDATE

UPDATE users SET password = 'thingesmimejoramigo', cellphone = '+1500111222' WHERE id = 3;

UPDATE products SET description = 'La ruedita más cómoda de tu ciudad' WHERE id = 2;

UPDATE products SET description = 'Las teclas más suaves del mercado', stock = 1 WHERE id = 3;

DELETE from products WHERE id = 8;

INSERT INTO products (name, description, price,stock)
VALUES
('Mate', 'Perfecto para Cebar', 100.00, 10);

TRUNCATE TABLE providers;

DROP TABLE providers;

SHOW TABLES;

-- SELECT

select * from products;

select * from products WHERE price >= 100;

select name, description, stock from products;

select name, description, stock from products where stock > 2;

select name, description, price, stock from products where name <> "mouse";

select name, description, price, stock from products where price BETWEEN 50 and 150;

select name from products where name like "%a%";

select name from products where id in (1,2,3);

-- ORDENAR

select * from products order by price DESC;

select * from products order by name asc;

select * from products order by price asc limit 3;

select name as producto, 
price as precio from products;

select name as nombre, price as final, stock as cantidad from products;

select u.name as usuario FROM users as u;

--FUNCIONES BASICAS

select count(*) as productos_total from products;

select count(*) as productos_con_stock from products where stock > 2; 

select sum(total) as ganancia_total from orders;

select avg(price) as precio_promedio from products;

select min(price) as precio_minimo, max(price) as precio_caro from products;


--JOINS

--INNER JOIN

select * from users;
select * from products;
select * from orders;

select users.name, users.id, orders.total 
from users 
inner join orders 
ON users.id = orders.user_id;

select 
u.name as comprador, 
o.id as pedido, 
o.total, 
o.order_date as fecha_de_compra
from users as u
inner join orders as o
ON u.id = o.user_id;

select 
u.name as comprador, 
p.name as producto,
o.quantity as cantidad, 
o.total
from users as u
inner join orders as o ON u.id = o.user_id
INNER JOIN products as p ON p.id = o.product_id;

--LEFT JOIN

select u.name as usuario, o.id as pedido, o.total
from users as u
LEFT JOIN orders as o 
on u.id = o.user_id;


select u.name as usuario, o.id as pedido, o.total
from users as u
LEFT JOIN orders as o 
on u.id = o.user_id
where o.id IS NULL;

--RIGHT JOIN

select u.name as usuario, o.id as pedido, o.total
from users as u
RIGHT JOIN orders as o 
on u.id = o.user_id;


--JOIN CON LAS 3 TABLAS

SELECT
u.name as usuario,
p.name as producto,
o.quantity as cantidad,
o.total,
o.order_date as fecha 
from orders as o 
join users as u on o.user_id = u.id 
join products as p on o.product_id = p.id
order by u.name;

--SUBCONSULTAS

select 
name, 
( select sum(total)
FROM orders
where user_id = u.id
) as total_gastado
from users as u;

--GROUP BY
select usuario, total_gastado
from (
	SELECT u.name as usuario, sum(o.total) as total_gastado
	from users as u 
	join orders as o on u.id = o.user_id
	group by u.id, u.name
) as resumen
where total_gastado > 100;

--HAVING
select 
name, 
( select sum(total)
FROM orders
where user_id = u.id
) as total_gastado
from users as u
HAVING total_gastado > 100;

--COALESCE

SELECT u.name as usuario, COALESCE(sum(total),"no compro nada") as total_gastado
from users as u 
left join orders as o on u.id = o.user_id 
group by u.id, u.name
order by total_gastado desc;
