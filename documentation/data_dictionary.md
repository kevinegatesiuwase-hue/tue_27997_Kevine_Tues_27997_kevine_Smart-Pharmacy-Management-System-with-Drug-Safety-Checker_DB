# Smart Pharmacy Management System – Database Architecture

This document describes the database data_dictionary of the Smart Pharmacy Management System, including all entities, attributes, data types, keys, and relationships implemented using Oracle 21c and PL/SQL.

---

## 🟦 1. MEDICATIONS

**Table Name:** `MEDICATIONS`

| Attribute             | Data Type     | Description                      | Constraints                  |
| --------------------- | ------------- | -------------------------------- | ---------------------------- |
| medication_id         | NUMBER        | Unique identifier for medication | Primary Key                  |
| name                  | VARCHAR2(100) | Brand name of medication         | NOT NULL                     |
| generic_name          | VARCHAR2(100) | Generic medication name          | NULL                         |
| requires_prescription | CHAR(1)       | Prescription required flag       | CHECK ('Y','N'), DEFAULT 'Y' |
| active_status         | CHAR(1)       | Medication availability status   | CHECK ('Y','N'), DEFAULT 'Y' |

**Description:** Stores master data for all medications managed by the pharmacy.

---

## 🟦 2. BATCHES

**Table Name:** `BATCHES`

| Attribute      | Data Type    | Description                | Constraints                              |
| -------------- | ------------ | -------------------------- | ---------------------------------------- |
| batch_id       | NUMBER       | Unique batch identifier    | Primary Key                              |
| medication_id  | NUMBER       | Related medication         | Foreign Key → MEDICATIONS(medication_id) |
| batch_number   | VARCHAR2(50) | Manufacturer batch number  | NOT NULL                                 |
| expiry_date    | DATE         | Expiry date of the batch   | NOT NULL                                 |
| qty_received   | NUMBER       | Quantity received          | NOT NULL                                 |
| qty_on_hand    | NUMBER       | Current available quantity | NOT NULL                                 |
| purchase_price | NUMBER(10,2) | Unit purchase price        | NULL                                     |

**Description:** Manages inventory at batch and expiry level for FIFO dispensing.

---

## 🟦 3. PATIENTS

**Table Name:** `PATIENTS`

| Attribute  | Data Type      | Description               | Constraints |
| ---------- | -------------- | ------------------------- | ----------- |
| patient_id | NUMBER         | Unique patient identifier | Primary Key |
| name       | VARCHAR2(100)  | Patient full name         | NOT NULL    |
| dob        | DATE           | Date of birth             | NULL        |
| phone      | VARCHAR2(20)   | Contact number            | NULL        |
| allergies  | VARCHAR2(4000) | Known drug allergies      | NULL        |

**Description:** Stores patient information used for safety validation.

---

## 🟦 4. PRESCRIPTIONS

**Table Name:** `PRESCRIPTIONS`

| Attribute       | Data Type     | Description                    | Constraints                        |
| --------------- | ------------- | ------------------------------ | ---------------------------------- |
| prescription_id | NUMBER        | Unique prescription identifier | Primary Key                        |
| patient_id      | NUMBER        | Patient linked to prescription | Foreign Key → PATIENTS(patient_id) |
| prescriber_name | VARCHAR2(100) | Name of prescribing doctor     | NOT NULL                           |
| date            | DATE          | Prescription date              | DEFAULT SYSDATE                    |
| status          | VARCHAR2(20)  | Prescription status            | PENDING / DISPENSED / CANCELLED    |

**Description:** Represents prescriptions issued to patients.

---

## 🟦 5. PRESCRIPTION_LINES

**Table Name:** `PRESCRIPTION_LINES`

| Attribute           | Data Type    | Description                 | Constraints                                  |
| ------------------- | ------------ | --------------------------- | -------------------------------------------- |
| line_id             | NUMBER       | Unique line identifier      | Primary Key                                  |
| prescription_id     | NUMBER       | Parent prescription         | Foreign Key → PRESCRIPTIONS(prescription_id) |
| medication_id       | NUMBER       | Prescribed medication       | Foreign Key → MEDICATIONS(medication_id)     |
| dosage              | VARCHAR2(50) | Dosage instructions         | NULL                                         |
| quantity_prescribed | NUMBER       | Quantity prescribed         | NOT NULL                                     |
| quantity_dispensed  | NUMBER       | Quantity actually dispensed | NULL                                         |

**Description:** Stores medication-level prescription details.

---

## 🟦 6. DRUG_INTERACTIONS

**Table Name:** `DRUG_INTERACTIONS`

| Attribute          | Data Type      | Description               | Constraints                              |
| ------------------ | -------------- | ------------------------- | ---------------------------------------- |
| interaction_id     | NUMBER         | Unique interaction record | Primary Key                              |
| med1_id            | NUMBER         | First medication          | Foreign Key → MEDICATIONS(medication_id) |
| med2_id            | NUMBER         | Second medication         | Foreign Key → MEDICATIONS(medication_id) |
| severity           | VARCHAR2(20)   | Interaction severity      | LOW / MEDIUM / HIGH                      |
| description        | VARCHAR2(4000) | Interaction details       | NULL                                     |
| recommended_action | VARCHAR2(4000) | Recommended action        | NULL                                     |

**Description:** Defines safety rules for drug–drug interactions.

---

## 🟦 7. DISPENSE_LOG

**Table Name:** `DISPENSE_LOG`

| Attribute       | Data Type      | Description                  | Constraints                                  |
| --------------- | -------------- | ---------------------------- | -------------------------------------------- |
| dispense_id     | NUMBER         | Unique dispensing identifier | Primary Key                                  |
| prescription_id | NUMBER         | Related prescription         | Foreign Key → PRESCRIPTIONS(prescription_id) |
| line_id         | NUMBER         | Related prescription line    | Foreign Key → PRESCRIPTION_LINES(line_id)    |
| batch_id        | NUMBER         | Batch used for dispensing    | Foreign Key → BATCHES(batch_id)              |
| qty_dispensed   | NUMBER         | Quantity dispensed           | NOT NULL                                     |
| dispensed_by    | VARCHAR2(100)  | Pharmacist username          | NOT NULL                                     |
| dispensed_at    | TIMESTAMP      | Dispensing timestamp         | DEFAULT SYSTIMESTAMP                         |
| override_flag   | CHAR(1)        | Indicates override           | CHECK ('Y','N')                              |
| override_reason | VARCHAR2(4000) | Reason for override          | NULL                                         |

**Description:** Records every dispensing operation for audit and tracking.

---

## 🟦 8. AUDIT_LOG

**Table Name:** `AUDIT_LOG`

| Attribute   | Data Type     | Description                |
| ----------- | ------------- | -------------------------- |
| log_id      | NUMBER        | Unique audit record        |
| table_name  | VARCHAR2(50)  | Affected table             |
| operation   | VARCHAR2(10)  | INSERT / UPDATE / DELETE   |
| record_id   | NUMBER        | Affected record identifier |
| old_values  | CLOB          | Data before change         |
| new_values  | CLOB          | Data after change          |
| changed_by  | VARCHAR2(100) | User who made the change   |
| change_time | TIMESTAMP     | Timestamp of change        |

**Description:** Maintains full database auditability.

---

## 🟦 9. STOCK_ALERTS

**Table Name:** `STOCK_ALERTS`

| Attribute     | Data Type    | Description             |
| ------------- | ------------ | ----------------------- |
| alert_id      | NUMBER       | Unique alert identifier |
| medication_id | NUMBER       | Related medication      |
| alert_type    | VARCHAR2(20) | LOW_STOCK / NEAR_EXPIRY |
| qty_on_hand   | NUMBER       | Quantity at alert time  |
| alert_time    | TIMESTAMP    | Alert generation time   |
| resolved_flag | CHAR(1)      | Alert resolution status |

**Description:** Stores system-generated inventory alerts.

---

## 🟧 Relationship Summary

- **MEDICATIONS (1) → (∞) BATCHES**
- **PATIENTS (1) → (∞) PRESCRIPTIONS**
- **PRESCRIPTIONS (1) → (∞) PRESCRIPTION_LINES**
- **MEDICATIONS (1) → (∞) PRESCRIPTION_LINES**
- **MEDICATIONS (1) → (∞) DRUG_INTERACTIONS**
- **PRESCRIPTION_LINES (1) → (∞) DISPENSE_LOG**
- **BATCHES (1) → (∞) DISPENSE_LOG**
- **ALL TABLES → AUDIT_LOG (via triggers)**

---
