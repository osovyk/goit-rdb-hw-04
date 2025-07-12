# Домашнє завдання до Теми 4. DML та DDL команди. Складні SQL вирази

## Опис домашнього завдання

1. Створіть базу даних для керування бібліотекою книг згідно зі структурою, наведеною нижче. Використовуйте DDL-команди для створення необхідних таблиць та їх зв'язків.

Структура БД:

* Назва схеми — “LibraryManagement”

* Таблиця "authors":
```
author_id (INT, автоматично зростаючий PRIMARY KEY)
author_name (VARCHAR)
```
* Таблиця "genres":
```
genre_id (INT, автоматично зростаючий PRIMARY KEY)
genre_name (VARCHAR)
```
* Таблиця "books":
```
book_id (INT, автоматично зростаючий PRIMARY KEY)
title (VARCHAR)
publication_year (YEAR)
author_id (INT, FOREIGN KEY зв'язок з "Authors")
genre_id (INT, FOREIGN KEY зв'язок з "Genres")
```
* Таблиця "users":
```
user_id (INT, автоматично зростаючий PRIMARY KEY)
username (VARCHAR)
email (VARCHAR)
```
* Таблиця "borrowed_books":
```
borrow_id (INT, автоматично зростаючий PRIMARY KEY)
book_id (INT, FOREIGN KEY зв'язок з "Books")
user_id (INT, FOREIGN KEY зв'язок з "Users")
borrow_date (DATE)
return_date (DATE)
```

```
CREATE SCHEMA LibraryManagement;

USE LibraryManagement;

CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255) NOT NULL
);

CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR,
    author_id INT NOT NULL,
    genre_id INT NOT NULL,
    CONSTRAINT fk_books_author
        FOREIGN KEY (author_id) REFERENCES authors (author_id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,
    CONSTRAINT fk_books_genre
        FOREIGN KEY (genre_id) REFERENCES genres (genre_id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);

CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT NOT NULL,
    user_id INT NOT NULL,
    borrow_date DATE NOT NULL,
    return_date DATE,
    CONSTRAINT fk_borrowed_books_book
        FOREIGN KEY (book_id) REFERENCES books (book_id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,
    CONSTRAINT fk_borrowed_books_user
        FOREIGN KEY (user_id) REFERENCES users (user_id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);
```

2. Заповніть таблиці простими видуманими тестовими даними. Достатньо одного-двох рядків у кожну таблицю.

```
INSERT INTO authors (author_name) VALUES
('Jane Austen'),
('Mark Twain'),
('Fyodor Dostoevsky'),
('Agatha Christie'),
('Jules Verne');

INSERT INTO genres (genre_name) VALUES
('Romance'),
('Adventure'),
('Mystery'),
('Science Fiction'),
('Classic');

INSERT INTO users (username, email) VALUES
('alice', 'alice@example.com'),
('bob', 'bob@example.org'),
('charlie', 'charlie@example.net'),
('diana', 'diana@example.com'),
('edward', 'edward@example.org');

INSERT INTO books (title, publication_year, author_id, genre_id) VALUES
('Pride and Prejudice', 1813, 1, 1),
('The Adventures of Tom Sawyer', 1876, 2, 2),
('Crime and Punishment', 1866, 3, 5),
('Murder on the Orient Express', 1934, 4, 3),
('Journey to the Center of the Earth', 1864, 5, 4);

INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date) VALUES
(1, 2, '2025-06-01', '2025-06-15'),
(3, 1, '2025-05-20', '2025-06-05'),
(5, 4, '2025-06-10', NULL),
(2, 5, '2025-04-12', '2025-04-30'),
(4, 3, '2025-06-20', NULL);
```

3. Перейдіть до бази даних, з якою працювали у темі 3. Напишіть запит за допомогою операторів FROM та INNER JOIN, що об’єднує всі таблиці даних, які ми завантажили з файлів: order_details, orders, customers, products, categories, employees, shippers, suppliers. Для цього ви маєте знайти спільні ключі. Перевірте правильність виконання запиту.
```
SELECT *
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;
```

4. Виконайте запити, перелічені нижче.

4.1. Визначте, скільки рядків ви отримали (за допомогою оператора COUNT).
```
SELECT COUNT(*) AS row_count
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;
```

4.2. Змініть декілька операторів INNER на LEFT чи RIGHT. Визначте, що відбувається з кількістю рядків. Чому? Напишіть відповідь у текстовому файлі.
```
SELECT COUNT(*) AS row_count
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
LEFT JOIN customers ON orders.customer_id = customers.id
LEFT JOIN employees ON orders.employee_id = employees.employee_id
LEFT JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;
```
**Заміна INNER JOIN на LEFT JOIN для таблиць customers, employees та shippers не змінює кількість рядків, бо всі ключі в orders мають відповідники в цих таблицях**

4.3. На основі запита з пункта 3 виконайте наступне: оберіть тільки ті рядки, де employee_id > 3 та ≤ 10.
```
SELECT COUNT(*) AS row_count
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10;
```

4.4. Згрупуйте за іменем категорії, порахуйте кількість рядків у групі, середню кількість товару (кількість товару знаходиться в order_details.quantity)
```
SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY categories.name;
```

4.5. Відфільтруйте рядки, де середня кількість товару більша за 21.
```
SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21;
```

4.6. Відсортуйте рядки за спаданням кількості рядків.
```
SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21
ORDER BY row_count DESC;
```

4.7. Виведіть на екран (оберіть) чотири рядки з пропущеним першим рядком.
```
SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21
ORDER BY row_count DESC
LIMIT 4 OFFSET 1;
```
