# Online Store PostgreSQL - Benjamin CUVILLIEZ
## _Mis à jour le 16/09/2024_

## Diagram :

![diagram](https://imgur.com/jM3NbDC.png "diagram")

# Command History and Explanations

This section provides all the commands used to set up the `online_store` PostgreSQL database.

**Legend:**
- `>` denotes user input.
- `>>>` denotes PostgreSQL output.

---

## List of Commands Executed for Database Setup

### 1. Connect to PostgreSQL as the `postgres` user
```bash
> psql -U postgres (password 0667)
```
### 2. Create database and connect (always as the `postgres` user)
```sql
CREATE DATABASE online_store;
\c online_store;
```
Output :
```sql
You are now connected to database "online_store" as user "postgres".
```
Explanation: Switches the current session to the online_store database to execute subsequent commands within this database.
### 3. Create tables (always as the `postgres` user)
__Customers__:
```sql
CREATE TABLE Customers (
    CustomerID SERIAL PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE NOT NULL,
    PhoneNumber VARCHAR(20),
    Address VARCHAR(100),
    City VARCHAR(50),
    State VARCHAR(50),
    PostalCode VARCHAR(20),
    Country VARCHAR(50),
    RegistrationDate DATE DEFAULT CURRENT_DATE
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Customers` table with customer information. The `CustomerID` is an auto-incrementing primary key. The `Email` field is unique and cannot be null.

__Manufacturers__:
```sql
CREATE TABLE Manufacturers (
    ManufacturerID SERIAL PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    ContactEmail VARCHAR(100),
    ContactPhone VARCHAR(20),
    Address VARCHAR(100)
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Manufacturers` table to store manufacturer details. `ManufacturerID` is the primary key.

__Categories__:
```sql
CREATE TABLE Categories (
    CategoryID SERIAL PRIMARY KEY,
    CategoryName VARCHAR(100) NOT NULL,
    Description TEXT
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Categories` table for product categorization. Each category has a unique `CategoryID`.

__Products__:
```sql
CREATE TABLE Products (
    ProductID SERIAL PRIMARY KEY,
    ProductName VARCHAR(100) NOT NULL,
    Description TEXT,
    Price DECIMAL(10, 2) NOT NULL,
    StockQuantity INTEGER DEFAULT 0,
    DateAdded DATE DEFAULT CURRENT_DATE,
    CategoryID INTEGER NOT NULL,
    ManufacturerID INTEGER NOT NULL,
    FOREIGN KEY (CategoryID) REFERENCES Categories(CategoryID) ON DELETE CASCADE,
    FOREIGN KEY (ManufacturerID) REFERENCES Manufacturers(ManufacturerID) ON DELETE CASCADE
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Products` table, storing product details. Foreign keys `CategoryID` and `ManufacturerID` link to the `Categories` and `Manufacturers` tables, respectively, enforcing referential integrity.

__Orders__:
```sql
CREATE TABLE Orders (
    OrderID SERIAL PRIMARY KEY,
    OrderDate DATE DEFAULT CURRENT_DATE,
    ShippingAddress VARCHAR(100),
    BillingAddress VARCHAR(100),
    TotalAmount DECIMAL(10, 2) NOT NULL,
    OrderStatus VARCHAR(20) DEFAULT 'Pending',
    CustomerID INTEGER NOT NULL,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID) ON DELETE CASCADE
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Orders` table to record customer orders. Each order references a customer via `CustomerID`.

__OrderItems__:
```sql
CREATE TABLE OrderItems (
    OrderItemID SERIAL PRIMARY KEY,
    Quantity INTEGER NOT NULL,
    UnitPrice DECIMAL(10, 2) NOT NULL,
    OrderID INTEGER NOT NULL,
    ProductID INTEGER NOT NULL,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID) ON DELETE CASCADE,
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID) ON DELETE CASCADE
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `OrderItems` table, detailing the products included in each order. Links to `Orders` and `Products` tables through foreign keys.

__Payments__:
```sql
CREATE TABLE Payments (
    PaymentID SERIAL PRIMARY KEY,
    PaymentDate DATE DEFAULT CURRENT_DATE,
    PaymentMethod VARCHAR(50),
    Amount DECIMAL(10, 2) NOT NULL,
    TransactionID VARCHAR(100),
    OrderID INTEGER NOT NULL,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID) ON DELETE CASCADE
);
```
Output:
```sql
CREATE TABLE
```
Explanation: Defines the `Payments` table to record payment information for orders. Each payment is linked to an order via `OrderID`.

### 4. Let's insert some data to the tables (always as the `postgres` user)

__Insert data into the `Customers` table__:
```sql
INSERT INTO Customers (FirstName, LastName, Email, PhoneNumber, Address, City, State, PostalCode, Country)
  VALUES
  ('Alice', 'Johnson', 'alice.johnson@example.com', '555-0100', '123 Maple Street', 'Springfield', 'IL', '62704', 'USA'),
  ('Bob', 'Williams', 'bob.williams@example.com', '555-0200', '456 Oak Avenue', 'Springfield', 'IL', '62705', 'USA'),
  ('Benjamin', 'Cuvilliez', 'cuvilliez@foi.hr', '06 38 37 47 54', '17 rue Georges Duhamel', 'Paris', 'Île-de-France', '75015', 'France'); 
```
Output : 
```sql
INSERT 0 3
```

__Insert data into the `Manufacturers` table__:
```sql
INSERT INTO Manufacturers (Name, ContactEmail, ContactPhone, Address)
  VALUES
  ('TechCorp', 'support@techcorp.com', '555-1000', '789 Tech Drive'),
  ('BookMakers Inc.', 'info@bookmakers.com', '555-2000', '321 Book Lane');
```
Output : 
```sql
INSERT 0 2
```

__Insert data into the `Categories` table__:
```sql
INSERT INTO Categories (CategoryName, Description)
  VALUES
  ('Electronics', 'Electronic gadgets and devices'),
  ('Books', 'Various genres of books');
```
Output : 
```sql
INSERT 0 2
```

__Insert data into the `Products` table__:
```sql
INSERT INTO Products (ProductName, Description, Price, StockQuantity, CategoryID, ManufacturerID)
  VALUES
  ('Smartphone X', 'A high-end smartphone with a sleek design', 899.99, 25, 1, 1),
  ('E-reader', 'A portable e-reader with a high-resolution display', 129.99, 50, 1, 1),
  ('Mystery Novel', 'An intriguing mystery novel by a bestselling author', 14.99, 100, 2, 2),
  ('Science Textbook', 'An in-depth science textbook for college students', 89.99, 40, 2, 2);
```
Output : 
```sql
INSERT 0 4
```

__Insert data into the `Orders` table__:
```sql
INSERT INTO Orders (ShippingAddress, BillingAddress, TotalAmount, OrderStatus, CustomerID)
  VALUES
  ('123 Maple Street', '123 Maple Street', 914.98, 'Processing', 1),
  ('456 Oak Avenue', '456 Oak Avenue', 144.98, 'Completed', 2),
  ('17 rue Georges Duhamel', '17 rue Georges Duhamel', 914.98, 'Completed', 3);
```
Output : 
```sql
INSERT 0 3
```

__Insert data into the `OrderItems` table__:
```sql
INSERT INTO OrderItems (Quantity, UnitPrice, OrderID, ProductID)
  VALUES
  (1, 899.99, 1, 1),
  (1, 14.99, 1, 3),
  (1, 129.99, 2, 2),
  (1, 14.99, 2, 3);
```
Output : 
```sql
INSERT 0 4
```

__Insert data into the `Payments` table__:
```sql
INSERT INTO Payments (PaymentMethod, Amount, TransactionID, OrderID)
  VALUES
  ('Credit Card', 914.98, 'TXN001', 1),
  ('PayPal', 144.98, 'TXN002', 2);
  ('PayPal', 914.98, 'TXN003', 3);
```
Output : 
```sql
INSERT 0 3
```
### 5.  Let's see the tables
#### 1. Customers Table

| Column           | Data Type      | Constraints               |
|------------------|----------------|---------------------------|
| **CustomerID**   | SERIAL         | PRIMARY KEY               |
| FirstName        | VARCHAR(50)    | NOT NULL                  |
| LastName         | VARCHAR(50)    | NOT NULL                  |
| Email            | VARCHAR(100)   | UNIQUE, NOT NULL          |
| PhoneNumber      | VARCHAR(20)    |                           |
| Address          | VARCHAR(100)   |                           |
| City             | VARCHAR(50)    |                           |
| State            | VARCHAR(50)    |                           |
| PostalCode       | VARCHAR(20)    |                           |
| Country          | VARCHAR(50)    |                           |
| RegistrationDate | DATE           | DEFAULT CURRENT_DATE      |

---

## 2. Manufacturers Table

| Column            | Data Type     | Constraints               |
|-------------------|---------------|---------------------------|
| **ManufacturerID**| SERIAL        | PRIMARY KEY               |
| Name              | VARCHAR(100)  | NOT NULL                  |
| ContactEmail      | VARCHAR(100)  |                           |
| ContactPhone      | VARCHAR(20)   |                           |
| Address           | VARCHAR(100)  |                           |

---

## 3. Categories Table

| Column          | Data Type     | Constraints               |
|-----------------|---------------|---------------------------|
| **CategoryID**  | SERIAL        | PRIMARY KEY               |
| CategoryName    | VARCHAR(100)  | NOT NULL                  |
| Description     | TEXT          |                           |

---

## 4. Products Table

| Column           | Data Type     | Constraints               |
|------------------|---------------|---------------------------|
| **ProductID**    | SERIAL        | PRIMARY KEY               |
| ProductName      | VARCHAR(100)  | NOT NULL                  |
| Description      | TEXT          |                           |
| Price            | DECIMAL(10,2) | NOT NULL                  |
| StockQuantity    | INTEGER       | DEFAULT 0                 |
| DateAdded        | DATE          | DEFAULT CURRENT_DATE      |
| CategoryID       | INTEGER       | NOT NULL, FOREIGN KEY     |
| ManufacturerID   | INTEGER       | NOT NULL, FOREIGN KEY     |

---

## 5. Orders Table

| Column           | Data Type     | Constraints               |
|------------------|---------------|---------------------------|
| **OrderID**      | SERIAL        | PRIMARY KEY               |
| OrderDate        | DATE          | DEFAULT CURRENT_DATE      |
| ShippingAddress  | VARCHAR(100)  |                           |
| BillingAddress   | VARCHAR(100)  |                           |
| TotalAmount      | DECIMAL(10,2) | NOT NULL                  |
| OrderStatus      | VARCHAR(20)   | DEFAULT 'Pending'         |
| CustomerID       | INTEGER       | NOT NULL, FOREIGN KEY     |

---

## 6. OrderItems Table

| Column          | Data Type     | Constraints               |
|-----------------|---------------|---------------------------|
| **OrderItemID** | SERIAL        | PRIMARY KEY               |
| Quantity        | INTEGER       | NOT NULL                  |
| UnitPrice       | DECIMAL(10,2) | NOT NULL                  |
| OrderID         | INTEGER       | NOT NULL, FOREIGN KEY     |
| ProductID       | INTEGER       | NOT NULL, FOREIGN KEY     |

---

## 7. Payments Table

| Column          | Data Type     | Constraints               |
|-----------------|---------------|---------------------------|
| **PaymentID**   | SERIAL        | PRIMARY KEY               |
| PaymentDate     | DATE          | DEFAULT CURRENT_DATE      |
| PaymentMethod   | VARCHAR(50)   |                           |
| Amount          | DECIMAL(10,2) | NOT NULL                  |
| TransactionID   | VARCHAR(100)  |                           |
| OrderID         | INTEGER       | NOT NULL, FOREIGN KEY     |


### 6. Some simples queries

__1 Retrieve all orders for customer 'Benjamin Cuvilliez':__
```sql
SELECT o.OrderID, o.OrderDate, o.TotalAmount, o.OrderStatus
  FROM Orders o
  JOIN Customers c ON o.CustomerID = c.CustomerID
  WHERE c.Email = 'cuvilliez@foi.hr';
``` 
Output:
```sql
      orderid |  orderdate  | totalamount | orderstatus 
     ---------+-------------+-------------+-------------
            1 | 2024-09-16  |      914.98 | Completed
     (1 row)
```
Screenshot:
!["screen1"](https://imgur.com/zWZIn3V.png "image1")

__2 List all products manufactured by 'TechCorp':__
```sql
SELECT p.ProductName, p.Description, p.Price, p.StockQuantity
  FROM Products p
  JOIN Manufacturers m ON p.ManufacturerID = m.ManufacturerID
  WHERE m.Name = 'TechCorp';
``` 
Output:
```sql
       productname  |               description               |  price  | stockquantity 
     ---------------+-----------------------------------------+---------+---------------
     Smartphone X   | A high-end smartphone with a sleek d... |  899.99 |            25
     E-reader       | A portable e-reader with a high-reso... |  129.99 |            50
     (2 rows)
```
Screenshot:
!["screen1"](https://imgur.com/bJ12MsO.png "image1")

__3 Calculate total sales per product:__
```sql
SELECT p.ProductName, SUM(oi.Quantity * oi.UnitPrice) AS TotalSales
  FROM OrderItems oi
  JOIN Products p ON oi.ProductID = p.ProductID
  GROUP BY p.ProductName;
``` 
Output:
```sql
       productname  | totalsales 
     ---------------+------------
     Smartphone X   |    1799.98
     Mystery Novel  |      44.97
     E-reader       |     129.99
     (3 rows)
```
Screenshot:
!["screen1"](https://imgur.com/lCkn0p3.png "image1")

__4 Get order details including items for Order ID 3:__
```sql
SELECT o.OrderID, o.OrderDate, c.FirstName || ' ' || c.LastName AS CustomerName,
         p.ProductName, oi.Quantity, oi.UnitPrice
  FROM Orders o
  JOIN Customers c ON o.CustomerID = c.CustomerID
  JOIN OrderItems oi ON o.OrderID = oi.OrderID
  JOIN Products p ON oi.ProductID = p.ProductID
  WHERE o.OrderID = 3;
``` 
Output:
```sql
 orderid | orderdate  |    customername    |  productname  | quantity | unitprice
---------+------------+--------------------+---------------+----------+-----------
       3 | 2024-09-16 | Benjamin Cuvilliez | Smartphone X  |        1 |    899.99
       3 | 2024-09-16 | Benjamin Cuvilliez | Mystery Novel |        1 |     14.99
(2 rows)
```
Screenshot:
!["screen1"](https://imgur.com/0TNudnJ.png "image1")

__5 List customers and the number of orders they've placed::__
```sql
SELECT c.FirstName || ' ' || c.LastName AS CustomerName, COUNT(o.OrderID) AS NumberOfOrders
  FROM Customers c
  LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerID
  ORDER BY NumberOfOrders DESC;
``` 
Output:
```sql
    customername    | numberoforders
--------------------+----------------
 Bob Williams       |              1
 Benjamin Cuvilliez |              1
 Alice Johnson      |              1
(3 rows)
```
Screenshot:
!["screen1"](https://imgur.com/s05MU29.png "image1")
