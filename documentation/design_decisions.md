# Smart Pharmacy Management System – Design Decisions

## 1. Overview

This document explains the major design decisions made during the development of the Smart Pharmacy Management System with Drug Safety Checker.  
It covers database design choices, PL/SQL procedures and packages, transaction handling, safety enforcement, auditing, and inventory management strategies.

---

## 2. Database Design Decisions

### 2.1 Relational Database

- **Choice:** Oracle RDBMS with PL/SQL.
- **Rationale:**
  - Provides strong transactional integrity (ACID compliance).
  - Supports triggers, procedures, functions, and packages.
  - Enables centralized enforcement of safety and audit rules.
  - Suitable for healthcare systems requiring reliability and compliance.

---

### 2.2 Medications Table

- **Design:** Stores master medication data (name, generic name, prescription flag).
- **Decision:** Include `requires_prescription` and `active_status`.
- **Rationale:**
  - Prevents dispensing non-approved medications.
  - Allows soft deactivation without deleting historical data.

---

### 2.3 Batches Table

- **Design:** Inventory managed at batch and expiry level.
- **Decision:** Include `expiry_date`, `qty_received`, and `qty_on_hand`.
- **Rationale:**
  - Enables FIFO dispensing by expiry date.
  - Prevents dispensing expired medications.
  - Supports accurate stock tracking and waste reduction.

---

### 2.4 Patients Table

- **Design:** Stores patient demographics and allergy information.
- **Decision:** Keep allergies as a text attribute.
- **Rationale:**
  - Allows flexible recording of allergy data.
  - Supports real-time allergy checks during dispensing.

---

### 2.5 Prescriptions & Prescription Lines

- **Design:** Split into `PRESCRIPTIONS` (header) and `PRESCRIPTION_LINES` (details).
- **Decision:** One prescription can contain multiple medications.
- **Rationale:**
  - Normalized design.
  - Reflects real-world prescription structure.
  - Simplifies reporting and safety checks per medication.

---

#
