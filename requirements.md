# 🔧 Backend Feature Requirement Specifications

This document details the **functional** and **non-functional requirements**, API endpoints, input/output specifications, validation rules, and performance criteria for key backend features of the **Airbnb Clone** project.

---

## 1. 🛡️ User Authentication

### 1.1. ✅ User Registration

**Functional Requirements:**
- Users can register with first name, last name, email, and password.
- Passwords are securely hashed.
- Default role is `guest`.
- Unique `user_id` is generated.
- Duplicate emails are rejected.
- Registration timestamp is stored.

**API Endpoint:**
- `POST /api/v1/users/register`

**Request Body:**
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "password": "securePassword123!"
}
```
**Successful Response:**
```json
{
  "user_id": "UUID",
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "role": "guest",
  "created_at": "ISO8601 timestamp"
}
```
**Error Response (Examples):**
```json
{ "error": "Email already registered." }
{
  "error": "Invalid input data.",
  "details": ["Email is required.", "Password must be at least 8 characters."]
}
```
**Validation Rules:**

- All fields required.
- Email: valid format, unique.
- Password: min 8 characters, must include uppercase, lowercase, number, special character.

**Performance:**
- Should complete within 500ms under 100 registrations/minute.

---
### 1.2. 🔐 User Login
**Functional Requirements:**

- Login with email and password.
- JWT issued on success.
- Invalid credentials rejected.

**API Endpoint:**
- POST /api/v1/users/login

**Request Body:**
```json
{
  "email": "john.doe@example.com",
  "password": "securePassword123!"
}
```
**Successful Response:**
```json
{
  "user_id": "UUID",
  "email": "john.doe@example.com",
  "role": "guest",
  "token": "JWT_TOKEN"
}
```
**Error Response:**
```json
{ "error": "Invalid email or password." }
```
**Performance:**
- Should complete within 300ms under 200 logins/minute.

---

## 2. 🏠 Property Management
### 2.1. 🏗️ Create Property Listing
**Functional Requirements:**
- Only authenticated host users can list properties.
- New listing associated with host’s user_id.
- Unique property_id is generated.
- Timestamps for creation and update.
**API Endpoint:**
- POST /api/v1/properties
- Requires Bearer token for host
**Request Body:**
```json
{
  "name": "Sunny Beachfront Villa",
  "description": "A spacious villa with direct beach access and stunning ocean views.",
  "location": "Miami Beach, FL",
  "pricepernight": 550.00
}
```
**Successful Response:**
```json
{
  "property_id": "UUID",
  "host_id": "UUID",
  "name": "Sunny Beachfront Villa",
  "description": "A spacious villa with direct beach access and stunning ocean views.",
  "location": "Miami Beach, FL",
  "pricepernight": 550.00,
  "created_at": "ISO8601 timestamp",
  "updated_at": "ISO8601 timestamp"
}
```
**Error Response Examples:**
```json
{ "error": "Authentication required." }
```
```json
{ "error": "Forbidden. Only hosts can list properties." }
```
```json
{
  "error": "Invalid input data.",
  "details": ["Name is required.", "Price per night must be positive."]
}
```
**Validation Rules:**
- All fields required.
- Price must be > 0.
- Role must be host.

**Performance:**
- Should complete within 600ms under 50 listings/minute.

## 3. 📆 Booking System
### 3.1. 📋 Create Booking
**Functional Requirements:**
- Authenticated guest users can book properties.
- System checks availability for requested dates.
- Total price calculated.
- Status is initially pending.
- Unique booking_id is generated.
**API Endpoint:**
- POST /api/v1/bookings
- Requires Bearer token for guest
**Request Body:**
```json
{
  "property_id": "UUID",
  "start_date": "2025-08-10",
  "end_date": "2025-08-15"
}
```
**Successful Response:**
```json
{
  "booking_id": "UUID",
  "property_id": "UUID",
  "user_id": "UUID",
  "start_date": "2025-08-10",
  "end_date": "2025-08-15",
  "total_price": 750.00,
  "status": "pending",
  "created_at": "ISO8601 timestamp"
}
```
**Error Response Examples:**
```json
{ "error": "Property not found." }
```
```json
{ "error": "Property not available for the selected dates." }
```
```json
{ "error": "Invalid date range." }
```
```json
{ "error": "Authentication required." }
```
**Validation Rules:**
- Valid UUID for property_id.
- Valid date format.
- start_date < end_date and in the future.
- User role must be guest.
**Performance:**
- Booking creation with availability check should complete within 800ms under 70 bookings/minute.