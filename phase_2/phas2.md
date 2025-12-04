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
_Diagram Legend:_

- **Rectangles:** Process steps
    
- **Diamonds:** Decision points
    
- **Dashed Lines:** Data flow
    
- **Swimlanes:** Separate roles (Patient, Pharmacist, System)
    

---

## Main Components

### Patient Swimlane:

1. **Start Event** → Patient arrives with prescription
    
2. **Submit Prescription** → Prescription entered into system
    
3. **Receive Medication** → Patient collects dispensed drugs
    
4. **End Event** → Patient departs
    

### Pharmacist Swimlane:

1. **Review Prescription** → Pharmacist verifies prescription details
    
2. **Decision:** Manual Override Needed?
    
    - **Yes:** Enter override reason (audit trail) → Approve dispensing
        
    - **No:** Approve dispensing directly
        

### System Swimlane:

1. **Validate Prescription** → Check against `PRESCRIPTIONS` and `PATIENTS`
    
2. **Check Drug Interactions & Allergies** → Query `DRUG_INTERACTIONS` and patient allergies
    
3. **Decision:** Interaction/Allergy Found?
    
    - **Yes:** Flag for pharmacist review
        
    - **No:** Reserve stock using FIFO (oldest expiry first)
        
4. **Dispense Medication** → Deduct from `BATCHES`, log in `DISPENSE_LOG`
    
5. **Update Inventory & Logs** → Update `AUDIT_LOG`, `STOCK_ALERTS`
    
6. **Decision:** Generate Alerts?
    
    - **Yes (Low stock/expiry):** Send alert to pharmacist
        
    - **No:** End process
        

---

##  MIS Functions

### 1. **Automated Safety Validation**

- Real-time drug–drug interaction checks
    
- Patient allergy verification before dispensing
    
- Prevents dangerous medication errors
    

### 2. **Inventory Optimization**

- Batch-level tracking with expiry dates
    
- FIFO allocation to reduce waste
    
- Automatic low-stock and near-expiry alerts
    

### 3. **Audit & Compliance**

- Full transaction logging (`AUDIT_LOG`, `DISPENSE_LOG`)
    
- Mandatory override reasons for regulatory compliance
    
- Traceable dispensing history per patient/medication
    

### 4. **Decision Support**

- Alerts for pharmacist intervention
    
- Real-time stock visibility
    
- Reporting on dispensing trends, expiry rates, interaction incidents
    

---

## Organizational Impact

### Benefits for Patients:

- **Enhanced Safety:** Reduced risk of allergic or interactive reactions
    
- **Trust & Transparency:** Clear audit trail of dispensed medications
    
- **Efficiency:** Faster, error-free dispensing
    

### Benefits for Pharmacy:

- **Regulatory Compliance:** Automated logging and override tracking
    
- **Reduced Waste:** FIFO expiry management minimizes expired stock
    
- **Operational Efficiency:** Automated checks reduce manual verification time
    
- **Risk Mitigation:** Fewer dispensing errors, lower liability
    

### Benefits for Healthcare System:

- **Data-Driven Insights:** Aggregate data on drug interactions, expiry rates
    
- **Standardized Processes:** Consistent safety checks across all pharmacies
    
- **Improved Public Health:** Safer medication practices at scale
    

---

## Analytics Opportunities

### 1. **Safety & Compliance Analytics**

- Frequency and type of drug interactions flagged
    
- Override rate analysis (why, by whom, for what)
    
- Allergy incident tracking and prevention
    

### 2. **Inventory Analytics**

- Expiry waste reduction metrics
    
- Stock turnover rates by medication
    
- Demand forecasting based on dispensing history
    

### 3. **Operational Analytics**

- Dispensing process efficiency (time per prescription)
    
- Pharmacist workload analysis (override handling time)
    
- Alert response time and resolution rates
    

### 4. **Predictive Analytics**

- Expiry prediction to optimize purchasing
    
- Interaction risk scoring for high-risk patient profiles
    
- Stockout prediction based on trends and lead times
    

---

## Technical Implementation

**Core Tables Involved:**

- `MEDICATIONS`, `BATCHES`, `PATIENTS`, `PRESCRIPTIONS`, `DRUG_INTERACTIONS`, `DISPENSE_LOG`, `AUDIT_LOG`, `STOCK_ALERTS`
    

**PL/SQL Automation:**

- Triggers for automatic safety checks
    
- Procedures for FIFO allocation and inventory updates
    
- Functions for interaction severity scoring
    
- Scheduled jobs for alert generation
    

---

## Conclusion

This **Smart Pharmacy Management System** transforms traditional pharmacy operations into an automated, safety-first, and audit-ready MIS. By integrating real-time drug safety checks, batch-level inventory control, and comprehensive logging, the system not only enhances patient safety but also improves operational efficiency and regulatory compliance. The BPMN model clearly delineates responsibilities between patient, pharmacist, and system, ensuring a streamlined, error-resistant dispensing workflow.



