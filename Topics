# Domain API vs System API

## Domain API
- **Purpose:** Exposes **business capabilities** for a specific domain (e.g., Payments, Customers, Payroll).
- **Focus:** Business language and rules (domain model). Designed to be stable and reusable.
- **Typical consumers:** Other applications/services that need the domain capability (experience/orchestration layers, other domains).
- **Data shape:** **Canonical/domain-oriented** (e.g., `SalaryPayment`, `Employee`), not tied to a specific backend system.
- **Logic:** Can include **business rules**, validations, enrichment, and domain-level orchestration.
- **Examples:**
  - `POST /payments/salary`
  - `GET /employees/{id}/payroll-details`

## System API
- **Purpose:** Provides access to an underlying **system of record** (core banking, ERP, CRM, database, mainframe).
- **Focus:** **Technical integration**—wraps the system’s constraints and interfaces.
- **Typical consumers:** Domain APIs or integration layers (usually not frontends directly).
- **Data shape:** **System-specific** and often mirrors backend schemas/fields.
- **Logic:** Minimal business logic; mostly mapping, protocol translation, auth, error normalization, throttling.
- **Examples:**
  - `POST /corebanking/payments` (close to core banking or ISO message fields)
  - `GET /sap/vendor/{id}`

## Quick comparison

| Aspect | Domain API | System API |
|---|---|---|
| Primary goal | Business capability | Backend system access |
| Vocabulary | Business/domain terms | System/technical terms |
| Coupling | Low (hides backend changes) | High (tied to one system) |
| Data model | Canonical | System-specific |
| Business logic | Often yes | Usually minimal |
| Consumers | Many channels/domains | Mainly internal integration |

## Rule of thumb
- Use a **Domain API** when you want a stable, business-centric interface.
- Use a **System API** when you need a clean, controlled wrapper around a specific backend system.
