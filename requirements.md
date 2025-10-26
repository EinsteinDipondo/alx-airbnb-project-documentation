# Technical Requirements Specification — Airbnb Clone Backend

This document describes the functional and non-functional requirements for key features of the Airbnb Clone Backend API. It acts as a developer-facing contract: endpoints, request/response shapes, validation rules, error responses, and performance targets.

Keep this document in sync with the API implementation and any public API docs. Use the examples below as canonical schemas for integration tests.

---

## Table of contents

- 1. User Authentication and Account Management  
  - 1.1 User Registration  
  - 1.2 User Login  
- 2. Property Listings Management  
  - 2.1 Create New Property Listing  
- 3. Booking and Reservation System  
  - 3.1 Create a New Reservation  
- Appendix: Common validation rules, status codes, and conventions

---

## Conventions

- All endpoints are rooted at `/api/v1/`.
- Dates use `YYYY-MM-DD` (ISO 8601 date).
- Times in error logs and traces use UTC.
- UUIDs are used for resource identifiers.
- All responses are JSON with appropriate HTTP status codes.
- Authentication uses JWT bearer tokens in the `Authorization: Bearer <token>` header when required.

---

# 1. User Authentication and Account Management

This feature allows users to register, authenticate, and manage profiles.

## 1.1 User Registration

- Endpoint: POST /api/v1/auth/register  
- Authorization: None (Public)  
- Purpose: Create a new user account and return an auth token.

Request body:
```json
{
  "email": "string",
  "password": "string",
  "first_name": "string",
  "last_name": "string",
  "role": "string (Guest|Host)"
}
```

Success (201 Created):
```json
{
  "token": "JWT_AUTH_TOKEN",
  "user": {
    "id": "uuid",
    "role": "Guest|Host",
    "email": "string"
  }
}
```

Errors:
- 400 Bad Request
```json
{ "message": "Email already registered." }
```
- 422 Unprocessable Entity — validation failures (see Validation Rules)

Validation Rules:
- Email: required, unique, valid email format.
- Password: required, minimum 8 characters, at least one uppercase letter, one number, and one symbol.
- Role: required, must be exactly `"Guest"` or `"Host"`.
- first_name and last_name: recommended, but validate length if present.

Performance:
- Target response time < 50 ms (exclude network latency). Measure in controlled environment; include unit/integration tests for latency-sensitive paths.

Notes:
- Create the user record, hash passwords (e.g., bcrypt/argon2), send verification email asynchronously (if required), and return token only after successful creation.

## 1.2 User Login

- Endpoint: POST /api/v1/auth/login  
- Authorization: None (Public)  
- Purpose: Authenticate user and return an auth token.

Request body:
```json
{
  "email": "string",
  "password": "string"
}
```

Success (200 OK):
```json
{
  "token": "JWT_AUTH_TOKEN",
  "user": {
    "id": "uuid",
    "role": "string",
    "email": "string",
    "profile_complete": true
  }
}
```

Errors:
- 401 Unauthorized
```json
{ "message": "Invalid credentials." }
```
- 422 Unprocessable Entity — missing fields

Validation Rules:
- Both email and password fields are required.
- Rate-limit authentication attempts and log suspicious activity.

Performance:
- Target response time < 50 ms.

Security:
- Use constant-time password checks, rotate JWT signing keys periodically, and set reasonable token expiry. Provide refresh tokens if necessary.

---

# 2. Property Listings Management

Hosts manage property listings: create, update, publish, and archive.

## 2.1 Create New Property Listing

- Endpoint: POST /api/v1/properties  
- Authorization: Required (Role: Host)  
- Purpose: Allow authenticated hosts to create listings.

Request body (example):
```json
{
  "title": "string",
  "description": "string",
  "price_per_night": 100.00,
  "location": "city, country",
  "amenities": ["WiFi", "Air conditioning", "Kitchen"],
  "guest_capacity": 4,
  "photos": ["https://cdn.example.com/...","https://cdn.example.com/...","https://cdn.example.com/..."]
}
```

Success (201 Created):
```json
{
  "id": "uuid",
  "host_id": "uuid",
  "title": "string",
  "status": "active"
}
```

Errors:
- 403 Forbidden
```json
{ "message": "User is not authorized to create listings." }
```
- 422 Unprocessable Entity — validation failures

Validation Rules:
- price_per_night: number, > 0.00
- guest_capacity: integer, > 0
- photos: array, minimum 3 entries (URLs or uploaded asset references)
- title and description: non-empty, reasonable length limits
- location: required, preferably normalized into structured fields (city, country, lat/lon optional)

Performance:
- Target response time < 100 ms (excluding time taken by external file storage/upload service). If photo uploads are synchronous, return an immediate 202 Accepted and process images asynchronously.

Authorization:
- Verify JWT and that the user role is Host; verify host owns or is permitted for the provided host_id if included.

Notes:
- Validate amenity taxonomy if one exists.
- Ensure idempotency for retries (e.g., accept client-supplied idempotency key).

---

# 3. Booking and Reservation System

Guests can create reservations that include synchronous payment processing.

## 3.1 Create a New Reservation

- Endpoint: POST /api/v1/bookings  
- Authorization: Required (Role: Guest)  
- Purpose: Create a confirmed reservation and process payment.

Request body:
```json
{
  "property_id": "uuid",
  "check_in_date": "YYYY-MM-DD",
  "check_out_date": "YYYY-MM-DD",
  "guest_count": 2,
  "payment_token": "string (Stripe/PayPal token)"
}
```

Success (201 Created):
```json
{
  "id": "uuid",
  "status": "confirmed",
  "total_price": 350.00,
  "payment_details": {
    "transaction_id": "string"
  }
}
```

Errors:
- 409 Conflict
```json
{ "message": "Property is not available for the selected dates." }
```
- 402 Payment Required
```json
{ "message": "Payment failed. Please check card details." }
```
- 422 Unprocessable Entity — validation failures

Validation Rules:
- Dates:
  - Must be valid ISO dates.
  - check_in_date and check_out_date must both be in the future.
  - check_out_date must be after check_in_date.
- Availability:
  - Verify property calendar is free for the entire stay.
  - Enforce minimum/maximum stay rules if configured.
- guest_count:
  - Must be integer and not exceed property guest_capacity.
- Payment:
  - payment_token must be present and validated by payment gateway call.

Performance:
- Target response time < 250 ms (this accounts for synchronous external payment gateway call). If payment gateway latency is higher, consider returning a 202 Accepted and confirm asynchronously, or implement a fast-fail pattern with retries and webhooks.

Atomicity:
- Reservation creation and payment charge must be atomic: either both succeed (confirmed) or both are rolled back (no booking persisted or booking is cancelled and charge refunded).
- Use idempotency keys for payment and booking creation to avoid double-charges on retries.

Security:
- Never log full payment tokens or card details. Use gateway-provided tokens and handle PCI scope minimization.

---

# Appendix — Status codes and common responses

- 200 OK — successful GET or permissible POST that returns existing resource
- 201 Created — resource created successfully
- 202 Accepted — request accepted and will be processed asynchronously
- 400 Bad Request — malformed request
- 401 Unauthorized — missing or invalid auth
- 403 Forbidden — valid auth but insufficient permissions
- 402 Payment Required — payment failed
- 409 Conflict — resource conflict (e.g., dates overlap)
- 422 Unprocessable Entity — validation failures
- 500 Internal Server Error — unexpected server-side error

Standard error response shape:
```json
{
  "message": "Human readable message",
  "code": "optional_machine_code",
  "errors": [
    { "field": "price_per_night", "message": "Must be greater than 0.00" }
  ]
}
```

---
