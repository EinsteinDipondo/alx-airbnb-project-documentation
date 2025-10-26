# 🏡 Use Case Diagram — Airbnb Clone Backend Documentation

This directory contains the Use Case Diagram and supporting README for the "alx-airbnb-project-documentation" — the System Analysis & Design phase for an Airbnb Clone backend.

## Project overview
The documentation in this repository captures the pre-development artifacts for a scalable, secure, and robust RESTful API backend for an Airbnb-like application. It simulates a real-world SDLC where design and planning precede implementation.

### Project goal
Translate high-level requirements into a complete, developer-ready blueprint for a RESTful API backend that supports:
- Secure authentication and authorization (JWT, RBAC)
- PostgreSQL-backed persistence
- Payment integration (e.g., Stripe)
- Scalable, testable, and maintainable architecture

---

## ✨ Core Functionalities & Technical Scope
Planned domains and highlights:
- **User Management** — Authentication, authorization, profile management (Guests, Hosts, Admin).
- **Property Listings** — Host CRUD, availability, search & filtering.
- **Booking Management** — Reservation creation, date validation, cancellations.
- **Payments & Financials** — Payment gateway integration and host payouts.
- **Reviews & Notifications** — Review system and transactional notifications (email/in-app).

---

## 📝 Documentation artifacts in this repository

| Artifact | Purpose | Status | Directory |
|---|---:|:---:|---|
| Features & Functionalities Overview | High-level map of features, technical & non-functional requirements | ✅ Completed | features-and-functionalities/ |
| Use Case Diagram | Visual model of actors and goals within the system | ✅ Completed | use-case-diagram/ |
| User Stories | Requirements from Guest / Host / Admin perspectives | ⏳ Pending | user-stories/ |
| Data Flow Diagram (DFD) | Visualize data movement through system processes | ⏳ Pending | system-diagrams/ |
| Flowcharts | Detailed logic & decision flows (Booking, Payment) | ⏳ Pending | system-diagrams/ |
| API Specifications | RESTful endpoint contracts (paths, request/response) | ⏳ Pending | api-specifications/ |

---

## Use Case Diagram
- File path: `use-case-diagram/use_case_diagram.png`  
- Description: Visual representation of the system boundary, primary actors (Unregistered User, Guest, Host, Admin, Payment Gateway), and their main goals (e.g., Log In, Create Listing, Submit Payment).

If viewing on GitHub, open the image directly:
![Use Case Diagram](./use_case_diagram.png)

---

## How to review
1. Clone the repository or browse on GitHub.
2. Open `use-case-diagram/use_case_diagram.png` to inspect the diagram.
3. Refer to `features-and-functionalities/features_and_functionalities_overview.png` for the feature map that complements the use case diagram.

---

## What's next
- Convert the Use Case Diagram into UML actors & use cases in the design docs, if desired.
- Draft user stories and API specifications (pending).
- Link use cases to concrete API endpoints and database entities during the design-to-implementation handoff.

---

## Contributing
Contributions and suggestions welcome — open an issue or submit a PR with improvements or clarifications to the diagrams and documentation.

---
Repository: EinsteinDipondo/alx-airbnb-project-documentation
Commit: 58ba80f8cf9453923b17e4e817871ad59daa710c
