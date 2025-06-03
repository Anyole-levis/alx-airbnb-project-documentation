# 🏡 Airbnb Clone – Backend Requirement Specifications

This document describes the backend specifications for core features of an Airbnb-style rental marketplace. It includes API endpoints, input/output formats, validation rules, and performance criteria.

---

## 📁 Table of Contents

* [🔐 User Authentication](#-user-authentication)
* [🏨 Property Management](#-property-management)
* [🗕️ Booking System](#-booking-system)

---

## 🔐 User Authentication

### Objective

Enable secure user registration, login, and token-based session management for guests and hosts.

---

### API Endpoints

#### `POST /api/auth/register`

* **Description**: Register a new user.
* **Input**

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass123",
  "role": "guest"
}
```

* **Validation Rules**:

  * `email`: Required, valid format, unique
  * `password`: Minimum 8 characters, must contain uppercase letter and number
  * `role`: One of `"guest"` or `"host"`
* **Response**

```json
{
  "message": "User registered successfully",
  "token": "JWT_TOKEN"
}
```

---

#### `POST /api/auth/login`

* **Description**: Log in a user.
* **Input**

```json
{
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

* **Response**

```json
{
  "token": "JWT_TOKEN",
  "user": {
    "id": 1,
    "name": "John Doe",
    "role": "guest"
  }
}
```

---

#### `GET /api/auth/profile`

* **Description**: Get user profile data.
* **Headers**: `Authorization: Bearer <JWT>`
* **Response**

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "guest"
}
```

---

### Performance Criteria

* Token generation: < 100ms
* Handle 1,000+ concurrent logins
* API response time: < 200ms (95th percentile)

---

## 🏨 Property Management

### Objective

Allow hosts to manage property listings with create, update, retrieve, and delete capabilities.

---

### API Endpoints

#### `POST /api/properties`

* **Description**: Create a new property listing.
* **Input**

```json
{
  "title": "Cozy Cabin",
  "description": "A quiet retreat in the woods.",
  "location": "Asheville, NC",
  "price_per_night": 120,
  "amenities": ["WiFi", "Kitchen", "Hot Tub"],
  "availability": {
    "start_date": "2025-06-01",
    "end_date": "2025-08-31"
  }
}
```

* **Validation Rules**:

  * All fields required
  * `price_per_night`: Must be > 0
  * `availability`: Valid date range
* **Response**

```json
{
  "id": 101,
  "message": "Listing created"
}
```

---

#### `PUT /api/properties/{id}`

* **Description**: Update property listing (host only).
* **Input**: Same as POST (optional fields allowed).
* **Authentication**: Required

---

#### `DELETE /api/properties/{id}`

* **Description**: Delete a property listing.
* **Authentication**: Required (host only)

---

#### `GET /api/properties`

* **Description**: Retrieve/search listings.
* **Query Parameters**:

  * `location`
  * `min_price`
  * `max_price`
  * `guests`
  * `amenities[]`
* **Response**: Paginated list of property objects

---

### Performance Criteria

* Support 50,000+ listings
* Search results returned in < 300ms
* Indexed search on `location`, `price`, `amenities`

---

## 🗕️ Booking System

### Objective

Allow guests to book properties, view bookings, and manage cancellations.

---

### API Endpoints

#### `POST /api/bookings`

* **Description**: Create a new booking.
* **Input**

```json
{
  "property_id": 101,
  "check_in": "2025-07-01",
  "check_out": "2025-07-05"
}
```

* **Validation Rules**:

  * Dates must be valid
  * Cannot overlap existing bookings
  * Guests cannot book their own property
* **Response**

```json
{
  "booking_id": 5001,
  "status": "pending"
}
```

---

#### `GET /api/bookings/{id}`

* **Description**: Retrieve booking details.
* **Authorization**: Guest, Host, or Admin

---

#### `PATCH /api/bookings/{id}/cancel`

* **Description**: Cancel a booking.
* **Output**

```json
{
  "message": "Booking cancelled",
  "status": "cancelled"
}
```

---

### Performance Criteria

* Prevent double bookings using DB transactions
* Check availability in < 150ms
* SLA: 99.9% consistency under load

---

> 📌 For more technical documentation (e.g., Swagger or OpenAPI specs), refer to `/docs` in the repository.
