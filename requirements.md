# 📋 Requirements Specification – Airbnb Clone Backend

This document details the technical and functional requirements for three core backend features: **User Authentication**, **Property Management**, and the **Booking System**. Each section includes API endpoints, input/output schemas, validation rules, and performance criteria.

---

## 1. User Authentication

### 1.1 Endpoints

| Method | Endpoint                   | Description                             |
| ------ | -------------------------- | --------------------------------------- |
| `POST` | `/api/v1/auth/register`    | Register a new user (guest or host)     |
| `POST` | `/api/v1/auth/login`       | Authenticate and issue JWT             |
| `POST` | `/api/v1/auth/logout`      | Invalidate current JWT                  |
| `GET`  | `/api/v1/auth/me`          | Retrieve current user profile           |

### 1.2 Request / Response

#### **Register** (`POST /api/v1/auth/register`)

- **Request Body** (JSON):
  ```json
  {
    "role": "guest" | "host",
    "firstName": "string",
    "lastName": "string",
    "email": "string",
    "password": "string"
  }
````

* **Response** (201 Created):

  ```json
  {
    "userId": "uuid",
    "firstName": "string",
    "lastName": "string",
    "email": "string",
    "role": "guest" | "host",
    "token": "JWT"
  }
  ```

#### **Login** (`POST /api/v1/auth/login`)

* **Request Body**:

  ```json
  {
    "email": "string",
    "password": "string"
  }
  ```
* **Response** (200 OK):

  ```json
  {
    "userId": "uuid",
    "email": "string",
    "role": "guest" | "host",
    "token": "JWT"
  }
  ```

#### **Get Profile** (`GET /api/v1/auth/me`)

* **Headers**: `Authorization: Bearer <JWT>`
* **Response** (200 OK):

  ```json
  {
    "userId": "uuid",
    "firstName": "string",
    "lastName": "string",
    "email": "string",
    "role": "guest" | "host",
    "profilePhotoUrl": "string",
    "preferences": { /* optional */ }
  }
  ```

### 1.3 Validation Rules

* **Email**: valid format, unique in `users` table
* **Password**: min. 8 characters, at least one uppercase, one lowercase, one digit
* **Role**: must be `guest` or `host`
* All required fields must be present and non-empty

### 1.4 Performance & Security

* **Average Response Time**: ≤ 200 ms under 100 RPS
* **Security**:

  * Use bcrypt (or Argon2) to hash passwords
  * JWT signed with strong secret, 1 hour expiry
  * Rate-limit registration/login endpoints (e.g., 5 requests/min)

---

## 2. Property Management

### 2.1 Endpoints

| Method   | Endpoint                                | Description                          |
| -------- | --------------------------------------- | ------------------------------------ |
| `GET`    | `/api/v1/properties`                    | List/search properties               |
| `GET`    | `/api/v1/properties/:propertyId`        | Get details of a single property     |
| `POST`   | `/api/v1/properties`                    | Create a new property listing (Host) |
| `PUT`    | `/api/v1/properties/:propertyId`        | Update an existing listing (Host)    |
| `DELETE` | `/api/v1/properties/:propertyId`        | Delete a listing (Host)              |
| `POST`   | `/api/v1/properties/:propertyId/images` | Upload property images (Host)        |

### 2.2 Request / Response

#### **Create Listing** (`POST /api/v1/properties`)

* **Headers**: `Authorization: Bearer <JWT>`
* **Body**:

  ```json
  {
    "title": "string",
    "description": "string",
    "location": {
      "address": "string",
      "city": "string",
      "country": "string",
      "coordinates": { "lat": number, "lng": number }
    },
    "pricePerNight": number,
    "maxGuests": integer,
    "amenities": ["Wi-Fi", "Pool", …],
    "availability": [
      { "from": "YYYY-MM-DD", "to": "YYYY-MM-DD" },
      …
    ]
  }
  ```
* **Response** (201 Created):

  ```json
  {
    "propertyId": "uuid",
    "hostId": "uuid",
    /* same fields as request */
  }
  ```

### 2.3 Validation Rules

* **pricePerNight**: > 0
* **maxGuests**: > 0 and ≤ 20
* **amenities**: non-empty array of known strings
* **availability**: non-overlapping date ranges, valid ISO dates

### 2.4 Performance & Security

* **Listing Retrieval**: ≤ 300 ms response with pagination (default pageSize=20)
* **Search**: indexed on `location`, `pricePerNight`, `maxGuests`
* Hosts may only CRUD their own listings (RBAC enforced)

---

## 3. Booking System

### 3.1 Endpoints

| Method  | Endpoint                             | Description                             |
| ------- | ------------------------------------ | --------------------------------------- |
| `POST`  | `/api/v1/bookings`                   | Create a new booking (Guest)            |
| `GET`   | `/api/v1/bookings/:bookingId`        | View a booking’s details                |
| `GET`   | `/api/v1/users/:userId/bookings`     | List all bookings for a user            |
| `PATCH` | `/api/v1/bookings/:bookingId/cancel` | Cancel an existing booking (Guest/Host) |

### 3.2 Request / Response

#### **Create Booking** (`POST /api/v1/bookings`)

* **Headers**: `Authorization: Bearer <JWT>`
* **Body**:

  ```json
  {
    "propertyId": "uuid",
    "startDate": "YYYY-MM-DD",
    "endDate": "YYYY-MM-DD",
    "paymentMethod": "stripe" | "paypal"
  }
  ```
* **Response** (201 Created):

  ```json
  {
    "bookingId": "uuid",
    "propertyId": "uuid",
    "guestId": "uuid",
    "startDate": "YYYY-MM-DD",
    "endDate": "YYYY-MM-DD",
    "status": "pending" | "confirmed",
    "totalPrice": number
  }
  ```

### 3.3 Validation Rules

* **Date Range**:

  * `endDate` > `startDate`
  * `startDate` and `endDate` must be in the future
  * No overlap with existing confirmed bookings (atomic DB transaction)
* **Payment Method**: must match available integrations
* **RBAC**: only guests can create/cancel their own bookings; hosts can cancel bookings on their listings

### 3.4 Performance & Reliability

* **Booking Creation**: ≤ 500 ms under 50 RPS, using transactional writes
* **Concurrency Control**: optimistic locking or row‐level locking to prevent double bookings
* **Notifications**: send confirmation emails/in-app alerts within 1 minute of booking event

---

## 📈 Summary of Non-Functional Criteria

| Feature             | Max Latency | RPS Support | Key Security Controls              |
| ------------------- | ----------- | ----------- | ---------------------------------- |
| Authentication      | 200 ms      | 100         | JWT, rate limiting, bcrypt hashing |
| Property Management | 300 ms      | 50          | RBAC, input sanitization, indexing |
| Booking System      | 500 ms      | 50          | Transactional integrity, locking   |

---

*End of requirements specification.*

````

---

Once you’ve reviewed this, add the file to your repo root as `requirements.md`, commit, and push:

```bash
git add requirements.md
git commit -m "Add detailed requirements specification for auth, properties, and bookings"
git push origin main
