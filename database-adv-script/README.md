# 🔗 SQL Join Queries – Airbnb Clone Project

## 🎯 Objective
This section documents key SQL join queries used to retrieve and analyze relationships between users, bookings, properties, and reviews in the Airbnb Clone backend system. The goal is to demonstrate real-world data retrieval techniques using INNER JOIN, LEFT JOIN, and FULL OUTER JOIN (simulated in MySQL).

---

## 📌 Queries Included

### 1️⃣ INNER JOIN: Bookings and Users

**Query Objective:**  
Retrieve all bookings along with the details of the users who made them.

```sql
SELECT 
    bookings.booking_id,
    bookings.property_id,
    bookings.start_date,
    bookings.end_date,
    bookings.total_price,
    bookings.status,
    bookings.created_at AS booking_created_at,
    users.user_id,
    users.first_name,
    users.last_name,
    users.email
FROM bookings
INNER JOIN users
    ON bookings.user_id = users.user_id;
````

**Use Case:**
This query helps in generating user-specific booking history and is useful for reporting, dashboards, or customer support.

---

### 2️⃣ LEFT JOIN: Properties and Reviews

**Query Objective:**
Retrieve all properties and their associated reviews, including properties that have no reviews.

```sql
SELECT 
    properties.property_id,
    properties.name AS property_name,
    properties.location,
    properties.pricepernight,
    reviews.review_id,
    reviews.rating,
    reviews.comment,
    reviews.created_at AS review_date,
    reviews.user_id AS reviewer_id
FROM properties
LEFT JOIN reviews
    ON properties.property_id = reviews.property_id;
```

**Use Case:**
Useful for displaying all listings on the platform, along with their reviews (if any), to site visitors or admins.

---

### 3️⃣ FULL OUTER JOIN (Simulated): Users and Bookings

> MySQL does not support `FULL OUTER JOIN` natively. This query simulates it using a `UNION` of `LEFT JOIN` and `RIGHT JOIN`.

**Query Objective:**
Retrieve all users and all bookings — including:

* Users with no bookings
* Bookings not linked to any user

```sql
-- Simulate FULL OUTER JOIN in MySQL
SELECT 
    users.user_id,
    users.first_name,
    users.last_name,
    users.email,
    bookings.booking_id,
    bookings.property_id,
    bookings.start_date,
    bookings.end_date,
    bookings.total_price,
    bookings.status
FROM users
LEFT JOIN bookings ON users.user_id = bookings.user_id

UNION

SELECT 
    users.user_id,
    users.first_name,
    users.last_name,
    users.email,
    bookings.booking_id,
    bookings.property_id,
    bookings.start_date,
    bookings.end_date,
    bookings.total_price,
    bookings.status
FROM bookings
RIGHT JOIN users ON users.user_id = bookings.user_id;
```

**Use Case:**
This query is especially helpful for auditing and data validation — ensuring all users and bookings are accounted for, even if they aren't matched.

---

## 📁 Directory Structure

```
alx-airbnb-project-documentation/
├── joins-and-queries/
│   ├── inner-join-bookings-users.sql
│   ├── left-join-properties-reviews.sql
│   ├── full-outer-join-users-bookings.sql
│   └── README.md
```

---

## 🛠️ Tools & Technologies

* SQL (MySQL 8+)
* Joins: INNER, LEFT, and simulated FULL OUTER
* UNION for result merging
* Real Airbnb-style relational schema

---

## ✅ Benefits of Using Joins

* Combine related data from multiple tables efficiently
* Enable powerful reporting and analysis
* Improve backend functionality (e.g., dashboards, filters)

---

## 👨‍💻 Author

This section was prepared as part of the ALX Software Engineering backend documentation track for the Airbnb Clone project.

---