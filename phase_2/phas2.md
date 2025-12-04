## Phase II: Business Process Modeling

**Student Name:** Gatesi Uwase Kevine  
**Project:** Smart Pharmacy Management System with Drug Safety Checker

---

## Business Process Overview

### Objective

To model the end-to-end prescription dispensing process within a pharmacy, ensuring safety checks, inventory management, and auditability are automated through PL/SQL-driven workflows.

### Scope

The process covers:

- Prescription submission and validation
    
- Automatic drug interaction and allergy checks
    
- Batch-level inventory management with FIFO allocation
    
- Dispensing with override handling (if needed)
    
- Audit logging and alert generation
    

### Key Players (Entities)

- **Patient:** Submits prescription, receives medication
    
- **Pharmacist:** Reviews and dispenses medication, handles overrides
    
- **System:** Automates validation, inventory updates, logging, and alerts
    
- **Data Sources:** `MEDICATIONS`, `BATCHES`, `PATIENTS`, `PRESCRIPTIONS`, `DRUG_INTERACTIONS`, `DISPENSE_LOG`, `AUDIT_LOG`
    

**MIS Relevance:**

- Automates pharmacy workflows
    
- Ensures medication safety and compliance
    
- Provides real-time inventory and alerting
    
- Maintains full auditability for regulatory requirements
    

**Expected Outcomes:**

- Safe, error-free dispensing
    
- Efficient inventory rotation (FIFO)
    
- Automated alerts for low stock/expiry
    
- Complete audit trail for all transactions
    

---

## 📊 Process Diagram (Swimlane BPMN)

<img width="2274" height="2368" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/ec9359e0-9de9-4270-afef-ffbff19cfff0" />
