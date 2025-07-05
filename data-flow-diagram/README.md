# 🔄 Data Flow Diagram (DFD) – Airbnb Clone Backend

This directory contains the **Data Flow Diagram (DFD)** for the backend system of the Airbnb Clone project. The diagram visualizes how data moves between key entities, processes, and data stores throughout the platform.

---

## 🎯 Objective

To map the **flow of data** within the system, showing how users interact with backend processes such as registration, property listings, bookings, and payments. This helps ensure that the backend design supports all functional requirements efficiently and clearly.

---

## 📌 What the Diagram Shows

The DFD illustrates:

- **External Entities**:
  - Guest / Host
  - Admin
  - Payment Gateway

- **Core Backend Processes**:
  - User Registration & Authentication
  - Property Management (Add/Edit/Delete Listings)
  - Search & Booking Process
  - Payment Handling
  - Review & Rating System
  - Admin Moderation

- **Data Stores**:
  - User Database
  - Listings Database
  - Bookings Database
  - Payments Database
  - Reviews Database

---

## 🧱 Example Data Flows Visualized

| Interaction                          | Data Flow Example                                       |
|--------------------------------------|----------------------------------------------------------|
| Guest → Book a Property              | Guest → Booking Process → Bookings DB + Listings DB     |
| Host → Add Listing                   | Host → Property Management → Listings DB                |
| Guest → Make Payment                 | Guest → Payment Gateway → Payments DB                   |
| Admin → Moderate Reviews             | Admin → Moderation Process → Reviews DB                 |
| Guest → Leave a Review               | Guest → Review System → Reviews DB                      |

---

## 📂 Files

| File Name       | Description                              |
|------------------|------------------------------------------|
| `data-flow.png` | Data Flow Diagram (Level 1) in PNG format |
| `README.md`     | This file describing the diagram          |

---

## 🛠 Tool Used

- Diagram created with [**Draw.io (diagrams.net)**](https://draw.io)
- Exported in high-resolution PNG format

---

## 📜 License

This work is developed for the **ALX Software Engineering Program** and is meant for educational and documentation purposes only.