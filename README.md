# Smart Pharmacy Management System with Drug Safety Checker

An Oracle PL/SQL–based backend system that helps pharmacies dispense medications safely by managing batch-level inventory, enforcing expiry-based FIFO dispensing, checking drug–drug interactions and patient allergies, and maintaining a complete audit trail for regulatory compliance.

---

## 👥 Project Identity

| Name                | Student ID |
|---------------------|------------|
| Gatesi Uwase Kevine | 27997      |

**Course:** PL/SQL  
**Lecturer:** Maniraguha Eric  
**Category:** Healthcare  

---

## 🧠 Problem Statement

Pharmacies often rely on manual or semi-automated systems that can result in:
- Dispensing expired medications  
- Ignoring drug–drug interactions  
- Poor batch-level inventory control  
- Lack of auditability and accountability  

These issues increase medication errors and put patient safety at risk.

---

## 🎯 Objectives

The Smart Pharmacy Management System aims to:

- Track medication inventory at **batch and expiry level**
- Enforce **FIFO dispensing** based on expiry dates
- Automatically check **drug–drug interactions**
- Validate prescriptions against **patient allergies**
- Generate alerts for **low stock** and **near-expiry batches**
- Maintain a **full audit trail** of all operations and overrides

---

## 🗄️ Core Database Tables

- **MEDICATIONS** (medication_id, name, generic_name, requires_prescription, active_status)
- **BATCHES** (batch_id, medication_id, batch_number, expiry_date, qty_received, qty_on_hand, purchase_price)
- **PATIENTS** (patient_id, name, dob, phone, allergies)
- **PRESCRIPTIONS** (prescription_id, patient_id, prescriber_name, date, status)
- **PRESCRIPTION_LINES** (line_id, prescription_id, medication_id, dosage, quantity_prescribed, quantity_dispensed)
- **DRUG_INTERACTIONS** (interaction_id, med1_id, med2_id, severity, description, recommended_action)
- **DISPENSE_LOG** (dispense_id, prescription_id, line_id, batch_id, qty_dispensed, dispensed_by, dispensed_at, override_flag, override_reason)
- **AUDIT_LOG** (log_id, table_name, operation, record_id, old_values, new_values, changed_by, change_time)
- **STOCK_ALERTS** (alert_id, medication_id, alert_type, qty_on_hand, alert_time, resolved_flag)

---

## 🚀 Quick Start Guide

### 1. Requirements

- Oracle Database 21c (XE or Standard)
- SQL Developer 23+
- A working Pluggable Database (PDB)

---

### 2. Start Oracle Services

Open **Command Prompt** and run:

```bash
net start OracleServiceXE
net start OracleXETNSListener
