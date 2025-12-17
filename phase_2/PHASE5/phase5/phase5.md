**PHASE V: TABLE IMPLEMENTATION & DATA INSERTION**

**Project:** Smart Pharmacy Management System with Drug Safety Checker  
**Category:** Healthcare  
**Student:** Gatesi Uwase Kevine  
**Lecturer:** Eric Maniraguha

### **Executive Summary**

Phase V involved the implementation of the physical database schema and insertion of comprehensive, realistic test data. This phase successfully created **9 normalized tables**, inserted **1,500+ rows** of operational pharmacy data, and verified all data integrity and business rules. The database is now fully prepared for the PL/SQL-driven safety logic and automation features in Phase VI.

### **Objectives Accomplished**

- Created 9 normalized database tables with proper constraints.
    
- Inserted 1,500+ rows of realistic pharmacy test data.
    
- Verified data integrity through comprehensive validation queries.
    
- Demonstrated all required query types (SELECT *, Joins, GROUP BY, Subqueries).
    
- Implemented and enforced critical business rules (FIFO, safety checks, audit trails).
    
- Prepared a robust database foundation for Phase VI PL/SQL development.
    

### **Database Schema Implementation**

#### **Tables Created**

1. **MEDICATIONS** – Core drug information (150+ rows)
    
2. **BATCHES** – Inventory tracked by expiry date (300+ rows)
    
3. **PATIENTS** – Patient demographics and allergies (200+ rows)
    
4. **PRESCRIPTIONS** – Prescription headers (400+ rows)
    
5. **PRESCRIPTION_LINES** – Individual prescribed medications (600+ rows)
    
6. **DRUG_INTERACTIONS** – Drug-drug interaction rules (50+ rows)
    
7. **DISPENSE_LOG** – Transaction log for dispensing (500+ rows)
    
8. **AUDIT_LOG** – System change audit trail (100+ rows)
    
9. **STOCK_ALERTS** – Low stock and expiry alerts (30+ rows)
    

#### **Key Constraints Implemented**

- **Primary Keys** on all tables (medication_id, batch_id, patient_id, etc.).
    
- **Foreign Keys** with appropriate referential actions (e.g., `ON DELETE RESTRICT` for prescriptions).
    
- **CHECK Constraints** for business rules (e.g., `expiry_date > SYSDATE`, `qty_on_hand >= 0`, `severity IN ('LOW','MEDIUM','HIGH')`).
    
- **NOT NULL Constraints** on critical fields (name, expiry_date, patient_id).
    
- **UNIQUE Constraints** (e.g., unique batch_number per medication).
    
- **DEFAULT Values** (e.g., `active_status DEFAULT 'ACTIVE'`, `resolved_flag DEFAULT 'N'`).
    

### **Data Insertion Details**

#### **Volume Requirements Met**

|Table|Rows Inserted|Requirement|Status|
|---|---|---|---|
|MEDICATIONS|150+|100+|**PASS**|
|BATCHES|300+|200+|**PASS**|
|PATIENTS|200+|100+|**PASS**|
|PRESCRIPTIONS|400+|200+|**PASS**|
|PRESCRIPTION_LINES|600+|400+|**PASS**|
|DRUG_INTERACTIONS|50+|30+|**PASS**|
|DISPENSE_LOG|500+|400+|**PASS**|
|AUDIT_LOG|100+|Sample|**PASS**|
|STOCK_ALERTS|30+|Sample|**PASS**|
|**TOTAL**|**1,500+**|**1,000+**|**PASS**|

#### **Data Characteristics**

- **Realistic Scenarios:** Based on actual pharmacy operations (dispensing, interactions, allergies, overrides).
    
- **Clinical Mix:** Various medication types (antibiotics, analgesics, antihypertensives) and patient conditions.
    
- **Edge Cases:** Includes NULL allergies, cancelled prescriptions, zero-quantity batches, and pharmacist overrides.
    
- **Business Rules:** All safety and inventory rules validated (FIFO, interaction blocking).
    
- **Referential Integrity:** All foreign key relationships are correctly maintained.
   ### **Query Type Demonstrations**
