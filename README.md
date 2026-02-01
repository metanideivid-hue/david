CREATE DATABASE BookstoreDB;
USE BookstoreDB;

CREATE TABLE Authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    country VARCHAR(50)
);

CREATE TABLE Books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(150) NOT NULL,
    genre VARCHAR(50),
    price DECIMAL(6,2),
    author_id INT,
    FOREIGN KEY (author_id) REFERENCES Authors(author_id)
);

CREATE TABLE Customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100),
    city VARCHAR(50)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    order_date DATE,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE Order_Details (
    order_detail_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    book_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (book_id) REFERENCES Books(book_id)
);
-- Authors
INSERT INTO Authors (name, country) VALUES
('George Orwell', 'UK'),
('J.K. Rowling', 'UK'),
('Haruki Murakami', 'Japan'),
('Stephen King', 'USA'),
('Agatha Christie', 'UK');

-- Books
INSERT INTO Books (title, genre, price, author_id) VALUES
('1984', 'Dystopian', 12.99, 1),
('Animal Farm', 'Satire', 10.99, 1),
('Harry Potter and the Sorcerer''s Stone', 'Fantasy', 15.49, 2),
('Kafka on the Shore', 'Fiction', 18.99, 3),
('The Shining', 'Horror', 14.99, 4);

-- Customers
INSERT INTO Customers (name, email, city) VALUES
('Maria Papadopoulou', 'maria@gmail.com', 'Athens'),
('Nikos Georgiou', 'nikos@yahoo.com', 'Thessaloniki'),
('Alexandros Kosta', 'alex@hotmail.com', 'Patra'),
('Eleni Nikolaou', 'eleniN@gmail.com', 'Athens'),
('Giannis Theodorou', 'johnTheo@yahoo.com', 'Larisa');

-- Orders
INSERT INTO Orders (order_date, customer_id) VALUES
('2024-02-10', 1),
('2024-02-11', 2),
('2024-03-01', 3),
('2024-03-05', 4),
('2024-03-07', 5);

-- Order_Details
INSERT INTO Order_Details (order_id, book_id, quantity) VALUES
(1, 1, 2),
(1, 3, 1),
(2, 4, 2),
(3, 2, 1),
(3, 5, 3);
-- 1️⃣ Λίστα βιβλίων με συγγραφέα
SELECT b.title, a.name AS author
FROM Books b
JOIN Authors a ON b.author_id = a.author_id;

-- 2️⃣ Παραγγελίες κάθε πελάτη
SELECT c.name, o.order_id, o.order_date
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id;

-- 3️⃣ Πελάτες και βιβλία που αγόρασαν
SELECT c.name, b.title, od.quantity
FROM Order_Details od
JOIN Orders o ON od.order_id = o.order_id
JOIN Customers c ON o.customer_id = c.customer_id
JOIN Books b ON od.book_id = b.book_id;

-- 4️⃣ Ποσό αγορών ανά πελάτη
SELECT c.name, SUM(od.quantity * b.price) AS total_spent
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Details od ON o.order_id = od.order_id
JOIN Books b ON od.book_id = b.book_id
GROUP BY c.name
ORDER BY total_spent DESC;

-- 5️⃣ Πωληθέντα βιβλία ανά είδος
SELECT b.genre, SUM(od.quantity) AS sold_copies
FROM Books b
JOIN Order_Details od ON b.book_id = od.book_id
GROUP BY b.genre;

-- 6️⃣ Μέση τιμή βιβλίων ανά συγγραφέα
SELECT a.name, AVG(b.price) AS average_price
FROM Authors a
JOIN Books b ON a.author_id = b.author_id
GROUP BY a.name
ORDER BY average_price DESC;
-- 1. Μέσος Όρος Βαθμών ανά Φοιτητή
WITH AvgGrades AS (
    SELECT student_id, AVG(grade) AS avg_grade
    FROM Enrollments
    GROUP BY student_id
)
SELECT * FROM AvgGrades;

-- 2. Άριστοι Φοιτητές (πάνω από τον συνολικό μέσο όρο)
WITH AvgGrades AS (
    SELECT student_id, AVG(grade) AS avg_grade FROM Enrollments GROUP BY student_id
),
TotalAvg AS (
    SELECT AVG(grade) AS overall_avg FROM Enrollments
)
SELECT s.name, a.avg_grade
FROM AvgGrades a
JOIN Students s ON a.student_id = s.student_id
JOIN TotalAvg t ON a.avg_grade > t.overall_avg;

-- 3. Μαθήματα με >5 Εγγραφές
WITH CourseCount AS (
    SELECT course_id, COUNT(student_id) AS total_enrolled
    FROM Enrollments
    GROUP BY course_id
)
SELECT c.title, cc.total_enrolled
FROM Courses c
JOIN CourseCount cc ON c.course_id = cc.course_id
WHERE total_enrolled > 5;

-- 4. Μέσος Όρος Βαθμών ανά Τμήμα
WITH DeptGrades AS (
    SELECT s.dept_id, AVG(e.grade) AS dept_avg
    FROM Enrollments e
    JOIN Students s ON e.student_id = s.student_id
    GROUP BY s.dept_id
)
SELECT d.name, dg.dept_avg
FROM Departments d
JOIN DeptGrades dg ON d.dept_id = dg.dept_id;

-- 5. Top 3 Φοιτητές ανά Τμήμα
WITH AvgGrades AS (
    SELECT s.student_id, s.dept_id, AVG(e.grade) AS avg_grade
    FROM Students s
    JOIN Enrollments e ON s.student_id = e.student_id
    GROUP BY s.student_id, s.dept_id
)
SELECT * FROM (
    SELECT *, RANK() OVER (PARTITION BY dept_id ORDER BY avg_grade DESC) AS rank
    FROM AvgGrades
) Ranked WHERE rank <= 3;
CREATE VIEW StudentPerformance AS
SELECT s.name, AVG(e.grade) AS avg_grade
FROM Students s
JOIN Enrollments e ON s.student_id = e.student_id
GROUP BY s.name;

CREATE VIEW DepartmentSummary AS
SELECT d.name, COUNT(s.student_id) AS total_students
FROM Departments d
LEFT JOIN Students s ON d.dept_id = s.dept_id
GROUP BY d.name;

CREATE VIEW CourseEnrollments AS
SELECT c.title, COUNT(e.student_id) AS enroll_count
FROM Courses c
LEFT JOIN Enrollments e ON c.course_id = e.course_id
GROUP BY c.title;

CREATE VIEW TopStudents AS
SELECT s.name, AVG(e.grade) AS avg_grade
FROM Students s
JOIN Enrollments e ON s.student_id = e.student_id
GROUP BY s.name
HAVING AVG(e.grade) > 8;

CREATE VIEW ProfessorsByDepartment AS
SELECT d.name AS department, p.name AS professor
FROM Professors p
JOIN Departments d ON p.dept_id = d.dept_id;
CREATE VIEW StudentPerformance AS
SELECT s.name, AVG(e.grade) AS avg_grade
FROM Students s
JOIN Enrollments e ON s.student_id = e.student_id
GROUP BY s.name;

CREATE VIEW DepartmentSummary AS
SELECT d.name, COUNT(s.student_id) AS total_students
FROM Departments d
LEFT JOIN Students s ON d.dept_id = s.dept_id
GROUP BY d.name;

CREATE VIEW CourseEnrollments AS
SELECT c.title, COUNT(e.student_id) AS enroll_count
FROM Courses c
LEFT JOIN Enrollments e ON c.course_id = e.course_id
GROUP BY c.title;

CREATE VIEW TopStudents AS
SELECT s.name, AVG(e.grade) AS avg_grade
FROM Students s
JOIN Enrollments e ON s.student_id = e.student_id
GROUP BY s.name
HAVING AVG(e.grade) > 8;

CREATE VIEW ProfessorsByDepartment AS
SELECT d.name AS department, p.name AS professor
FROM Professors p
JOIN Departments d ON p.dept_id = d.dept_id;
