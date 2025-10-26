# User Stories — Airbnb Clone Backend

This document captures the key user stories for the Airbnb clone backend. Each story is written from the user's perspective and includes short acceptance criteria to guide implementation and testing.

---

## Actors
- Guest (traveler searching and booking stays)
- Host (owner listing properties and managing bookings)
- Admin (platform moderator and operator)
- Unregistered User (visitor who has not yet created an account)

---

## 1. Account Registration and Authentication 🔐
As an Unregistered User, I want to sign up using my email/password or OAuth (Google/Facebook) so that I can establish my identity and access core platform features like booking and listing properties.

Acceptance criteria:
- Users can register with email and password.
- Users can authenticate via Google and Facebook OAuth.
- Passwords are stored securely (hashed).
- Sign-in returns an access token (JWT or similar) for authenticated requests.
- Error messages are clear for duplicate account, invalid email, weak password.

Priority: High

---

## 2. Property Search and Filtering 🔎
As a Guest, I want to search for properties by location, specify a date range, and filter by price and amenities so that I can quickly find accommodations that match my travel needs and budget.

Acceptance criteria:
- Search by city, coordinates, or address keywords.
- Filter by date range; unavailable listings for those dates are excluded.
- Filters for price range, number of guests, and amenities (Wi‑Fi, parking, etc.).
- Results include key listing metadata (price, rating, thumbnail, short description).
- Pagination and sorting (price, rating, distance).

Priority: High

---

## 3. Listing a Property 🏠
As a Host, I want to create a detailed listing by providing a description, setting the nightly price, uploading multiple photos, and defining my availability calendar so that potential guests have all the information they need.

Acceptance criteria:
- Hosts can create and edit listings with title, description, location, price, guest capacity, and amenities.
- Hosts can upload and manage multiple photos per listing.
- Hosts can set availability (calendar) and blackout dates.
- Validation for required fields and sensible price ranges.

Priority: High

---

## 4. Managing Earnings 💳
As a Host, I want to view a clear history of my earnings and automatically receive payouts so that I can manage my rental income efficiently without manual transaction tracking.

Acceptance criteria:
- Hosts have a dashboard showing past payouts, upcoming payouts, and fees/charges.
- Integrate with a payment processor (e.g., Stripe) for payouts.
- Detailed earning entries link to the reservation that generated them.
- Refunds and adjustments are recorded and visible.

Priority: Medium

---

## 5. Booking and Secure Payment ✅
As a Guest, I want to create a reservation for selected dates and securely submit payment via the integrated payment gateway so that my booking is confirmed and the dates are held.

Acceptance criteria:
- Guests can initiate a booking request for available dates and see total cost (including fees and taxes).
- Payments are processed securely through an integrated gateway (e.g., Stripe).
- Successful payment results in a confirmed reservation and calendar update for the listing.
- Clear error handling for declined payments and concurrency (double-booking) prevention.

Priority: High

---

## 6. Reviews and Ratings ⭐
As a Guest, I want to leave a review and rating for a property only after the stay is complete so that future guests have honest, verified feedback.

Acceptance criteria:
- Only guests with completed stays can submit reviews for that reservation.
- Reviews include a star rating and optional textual feedback.
- Hosts can respond to reviews.
- Reviews are listed on the property page, with moderation controls for Admins.

Priority: Medium

---

## 7. User Moderation and Admin Controls 🛡️
As an Admin, I want to view, audit, and manage user accounts (including changing roles or suspension) so that I can ensure platform safety and enforce policies.

Acceptance criteria:
- Admin dashboard lists users, roles, and account status.
- Admins can change roles (Guest, Host, Admin) and suspend/reactivate accounts.
- Audit logs record admin actions (role changes, suspensions).
- Admins can remove or hide problematic listings and reviews.

Priority: High

---

## Notes & Next Steps
- Convert each high-priority story into tasks and API endpoints.
- Add acceptance tests for authentication flows, booking race conditions, and payments.
- Design database schema and authorization rules to support roles and ownership.
