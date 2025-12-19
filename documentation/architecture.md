# Smart Pharmacy Management System – Data Dictionary

## 1. Overview

The Smart Pharmacy Management System is a relational database–backed system designed to ensure safe medication dispensing, batch-level inventory control, drug–drug interaction checking, allergy validation, and full auditability.  
This data architecture documents the core database tables, their attributes, and their roles within the system.

---

## 2. Database Architecture

### 2.1 Tables

---

### **MEDICATIONS**

Stores master information about medications.

| Column                | Data Type     | Constraints                  | Description                       |
| --------------------- | ------------- | ---------------------------- | --------------------------------- |
| medication_id         | NUMBER        | PRIMARY KEY                  | Unique medication identifier      |
| name                  | VARCHAR2(100) | NOT NULL                     | Medication brand name             |
| generic_name          | VARCHAR2(100) |                              | Generic name                      |
| requires_prescription | CHAR(1)       | CHECK ('Y','N'), DEFAULT 'Y' | Prescription requirement flag     |
| active_status         | CHAR(1)       | CHECK ('Y','N'), DEFAULT 'Y' | Indicates if medication is active |

---

### **BATCHES**

Tracks medication inventory at batch and expiry level.

| Column         | Data Type    | Constraints                     | Description                |
| -------------- | ------------ | ------------------------------- | -------------------------- |
| batch_id       | NUMBER       | PRIMARY KEY                     | Unique batch identifier    |
| medication_id  | NUMBER       | FK → MEDICATIONS(medication_id) | Related medication         |
| batch_number   | VARCHAR2(50) | NOT NULL                        | Manufacturer batch number  |
| expiry_date    | DATE         | NOT NULL                        | Expiry date of the batch   |
| qty_received   | NUMBER       | NOT NULL                        | Quantity received          |
| qty_on_hand    | NUMBER       | NOT NULL                        | Current quantity available |
| purchase_price | NUMBER(10,2) |                                 | Purchase price per unit    |

---

### **PATIENTS**

Stores patient details and allergy information.

| Column     | Data Type      | Constraints | Description               |
| ---------- | -------------- | ----------- | ------------------------- |
| patient_id | NUMBER         | PRIMARY KEY | Unique patient identifier |
| name       | VARCHAR2(100)  | NOT NULL    | Patient full name         |
| dob        | DATE           |             | Date of birth             |
| phone      | VARCHAR2(15)   |             | Contact phone number      |
| allergies  | VARCHAR2(4000) |             | Known drug allergies      |

---

### **PRESCRIPTIONS**

Represents prescriptions issued for patients.

| Column          | Data Type     | Constraints               | Description                    |
| --------------- | ------------- | ------------------------- | ------------------------------ |
| prescription_id | NUMBER        | PRIMARY KEY               | Unique prescription identifier |
| patient_id      | NUMBER        | FK → PATIENTS(patient_id) | Related patient                |
| prescriber_name | VARCHAR2(100) | NOT NULL                  | Prescribing doctor             |
| date            | DATE          | DEFAULT SYSDATE           | Prescription date              |
| status          | VARCHAR2(20)  |                           | PENDING, DISPENSED, CANCELLED  |

---

### **PRESCRIPTION_LINES**

Stores medication details per prescription.

| Column              | Data Type    | Constraints                         | Description                 |
| ------------------- | ------------ | ----------------------------------- | --------------------------- |
| line_id             | NUMBER       | PRIMARY KEY                         | Unique line identifier      |
| prescription_id     | NUMBER       | FK → PRESCRIPTIONS(prescription_id) | Related prescription        |
| medication_id       | NUMBER       | FK → MEDICATIONS(medication_id)     | Prescribed medication       |
| dosage              | VARCHAR2(50) |                                     | Dosage instructions         |
| quantity_prescribed | NUMBER       | NOT NULL                            | Quantity prescribed         |
| quantity_dispensed  | NUMBER       |                                     | Quantity actually dispensed |

---

### **DRUG_INTERACTIONS**

Defines drug–drug interaction rules.

| Column             | Data Type      | Constraints                     | Description                   |
| ------------------ | -------------- | ------------------------------- | ----------------------------- |
| interaction_id     | NUMBER         | PRIMARY KEY                     | Unique interaction identifier |
| med1_id            | NUMBER         | FK → MEDICATIONS(medication_id) | First medication              |
| med2_id            | NUMBER         | FK → MEDICATIONS(medication_id) | Second medication             |
| severity           | VARCHAR2(20)   |                                 | LOW, MEDIUM, HIGH             |
| description        | VARCHAR2(4000) |                                 | Interaction description       |
| recommended_action | VARCHAR2(4000) |                                 | Recommended safety action     |

---

### **DISPENSE_LOG**

Logs all medication dispensing actions.

| Column          | Data Type      | Constraints                         | Description               |
| --------------- | -------------- | ----------------------------------- | ------------------------- |
| dispense_id     | NUMBER         | PRIMARY KEY                         | Unique dispensing record  |
| prescription_id | NUMBER         | FK → PRESCRIPTIONS(prescription_id) | Related prescription      |
| line_id         | NUMBER         | FK → PRESCRIPTION_LINES(line_id)    | Related prescription line |
| batch_id        | NUMBER         | FK → BATCHES(batch_id)              | Batch used                |
| qty_dispensed   | NUMBER         | NOT NULL                            | Quantity dispensed        |
| dispensed_by    | VARCHAR2(100)  | NOT NULL                            | Pharmacist username       |
| dispensed_at    | TIMESTAMP      | DEFAULT SYSTIMESTAMP                | Dispensing time           |
| override_flag   | CHAR(1)        | CHECK ('Y','N')                     | Indicates override        |
| override_reason | VARCHAR2(4000) |                                     | Reason for override       |

---

### **AUDIT_LOG**

Maintains a full audit trail of database operations.

| Column      | Data Type     | Description                   |
| ----------- | ------------- | ----------------------------- |
| log_id      | NUMBER        | Primary key                   |
| table_name  | VARCHAR2(50)  | Affected table                |
| operation   | VARCHAR2(10)  | INSERT, UPDATE, DELETE        |
| record_id   | NUMBER        | Affected record identifier    |
| old_values  | CLOB          | Values before change          |
| new_values  | CLOB          | Values after change           |
| changed_by  | VARCHAR2(100) | User who performed the change |
| change_time | TIMESTAMP     | Time of change                |

---

### **STOCK_ALERTS**

Stores system-generated inventory alerts.

| Column        | Data Type    | Description                    |
| ------------- | ------------ | ------------------------------ |
| alert_id      | NUMBER       | Primary key                    |
| medication_id | NUMBER       | Related medication             |
| alert_type    | VARCHAR2(20) | LOW_STOCK, NEAR_EXPIRY         |
| qty_on_hand   | NUMBER       | Quantity at alert time         |
| alert_time    | TIMESTAMP    | Time alert was generated       |
| resolved_flag | CHAR(1)      | Indicates if alert is resolved |

---

## 3. Key Notes

- All dispensing and inventory operations are transaction-controlled.
- FIFO dispensing is enforced using expiry dates.
- Safety checks for allergies and drug interactions are handled at database level.
- Overrides require mandatory justification and are fully auditable.
- The audit log ensures regulatory compliance and traceability.

---
