ER DIAGRAM + NOMALIZATION PROCESS + DATA DICTIONARY + ASSUMPTIONS + BI CONSIDERATIONS
------
<img width="2039" height="1718" alt="Untitled Diagram drawio (1)" src="https://github.com/user-attachments/assets/96721464-1ab0-47ac-bfff-7717762411f3" />
 ## **NORMALIZATION PROCESS: FROM UNNORMALIZED TO 3NF**

### **For 0NF (Unnormalized Form):**

```

┌──────────────────────────────────────────────────────────────────────────┐
│ PHARMACY_DATA (Unnormalized)                                            │
├──────────────────────────────────────────────────────────────────────────┤
│ prescription_id                                                         │
│ patient_name                                                            │
│ patient_allergies (PROBLEM: "Penicillin, Aspirin")                      │
│ medications (PROBLEM: "DrugA, DrugB, DrugC")                            │
│ batches_used (PROBLEM: "B001:5, B002:10")                               │
│ interactions_found (PROBLEM: "DrugA-DrugB:Severe")                      │
└──────────────────────────────────────────────────────────────────────────┘
```
**Problems:**

- Multi-valued attributes (allergies, medications)
    
- Composite data in single columns
    
- No atomic values
    

### **For 1NF (First Normal Form - Atomic Values):**

```

┌──────────────────────────────────────────────────────────────────────────┐
│ PHARMACY_DATA_1NF                                                       │
├──────────────────────────────────────────────────────────────────────────┤
│ prescription_id                                                         │
│ patient_id                                                              │
│ allergy (FIXED: One value per row)                                      │
│ medication_id (FIXED: One value per row)                                │
│ batch_id (FIXED: One value per row)                                     │
│ interaction_id (FIXED: One value per row)                               │
└──────────────────────────────────────────────────────────────────────────┘
```
**Improvements:**

- All columns contain atomic values
    
- Still has redundancy (patient info repeated)
    

### **For 2NF (Second Normal Form - Remove Partial Dependencies):**

```

┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  PATIENTS    │      │ MEDICATIONS  │      │  BATCHES     │
├──────────────┤      ├──────────────┤      ├──────────────┤
│ patient_id PK│      │ medication_id│      │ batch_id PK  │
│ name         │      │ name         │      │ medication_id│
│ dob          │      │ generic_name │      │ batch_number │
│ phone        │      │              │      │ expiry_date  │
│ allergies    │      └──────────────┘      │ qty_on_hand  │
└──────────────┘              ↑             └──────────────┘
       │                      │                      ↑
       │                      │                      │
       │ 1                    │ 1                    │ 1
       ▼ N                    ▼ M                    ▼ M
┌──────────────┐      ┌─────────────────────┐     ┌──────────────┐
│PRESCRIPTIONS │      │DRUG_INTERACTIONS    │     │DISPENSE_LOG  │
├──────────────┤      ├─────────────────────┤     ├──────────────┤
│prescription_id│     │interaction_id PK    │     │dispense_id PK│
│patient_id FK │     │med1_id FK           │     │batch_id FK   │
│prescriber_name│     │med2_id FK           │     │prescription_id│
│status        │     │severity             │     │qty_dispensed │
└──────────────┘     └─────────────────────┘     └──────────────┘
```
**Improvements:**

- Removed partial dependencies
    
- Each non-key attribute depends on the whole primary key
    
- Created separate tables for independent entities
    

### **For 3NF (Third Normal Form - Remove Transitive Dependencies):**     
```

┌─────────────────────────────────────────────────────────┐
│                    PATIENTS                            │
├─────────────────────────────────────────────────────────┤
│ patient_id (PK)        NUMBER                          │
│ first_name             VARCHAR2(50)                    │
│ last_name              VARCHAR2(50)                    │
│ dob                    DATE                            │
│ phone                  VARCHAR2(20)                    │
│ email                  VARCHAR2(100)                   │
│                                                        │
│ Constraints:                                           │
│ - PK: patient_id                                       │
│ - NOT NULL: first_name, last_name, dob                │
│ - UNIQUE: email                                        │
│ - CHECK: dob <= SYSDATE - (13*365) [Age >= 13]        │
└─────────────────────────────────────────────────────────┘
            │
            │ 1
            │
            │
            │ N
            ▼
┌─────────────────────────────────────────────────────────┐
│                  PRESCRIPTIONS                          │
├─────────────────────────────────────────────────────────┤
│ prescription_id (PK)   NUMBER                          │
│ patient_id (FK)        NUMBER                          │
│ prescriber_name        VARCHAR2(100)                   │
│ prescriber_license     VARCHAR2(50)                    │
│ prescription_date      DATE                            │
│ status                 VARCHAR2(20)                    │
│                                                        │
│ Constraints:                                           │
│ - PK: prescription_id                                  │
│ - FK: patient_id → PATIENTS                           │
│ - CHECK: status IN ('PENDING','APPROVED','DISPENSED',  │
│                     'CANCELLED','EXPIRED')             │
│ - CHECK: prescription_date <= SYSDATE                  │
└─────────────────────────────────────────────────────────┘
            │ 1
            │
            │
            │ N
            ▼
┌─────────────────────────────────────────────────────────┐
│               PRESCRIPTION_LINES                        │
├─────────────────────────────────────────────────────────┤
│ line_id (PK)           NUMBER                          │
│ prescription_id (FK)   NUMBER                          │
│ medication_id (FK)     NUMBER                          │
│ dosage                 VARCHAR2(50)                    │
│ quantity_prescribed    NUMBER                          │
│ quantity_dispensed     NUMBER                          │
│                                                        │
│ Constraints:                                           │
│ - PK: line_id                                          │
│ - FK: prescription_id → PRESCRIPTIONS                 │
│ - FK: medication_id → MEDICATIONS                      │
│ - CHECK: quantity_prescribed > 0                      │
│ - CHECK: quantity_dispensed <= quantity_prescribed     │
│ - DEFAULT: quantity_dispensed = 0                     │
└─────────────────────────────────────────────────────────┘
            ▲
            │ N
            │
            │
            │ 1
            │
┌─────────────────────────────────────────────────────────┐
│                  MEDICATIONS                           │
├─────────────────────────────────────────────────────────┤
│ medication_id (PK)     NUMBER                          │
│ name                   VARCHAR2(100)                   │
│ generic_name           VARCHAR2(100)                   │
│ requires_prescription  CHAR(1)                         │
│ active_status          CHAR(1)                         │
│                                                        │
│ Constraints:                                           │
│ - PK: medication_id                                    │
│ - NOT NULL: name, requires_prescription, active_status │
│ - CHECK: requires_prescription IN ('Y','N')           │
│ - CHECK: active_status IN ('A','I')                   │
└─────────────────────────────────────────────────────────┘
            │ 1
            │
            │
            │ N
            ▼
┌─────────────────────────────────────────────────────────┐
│                    BATCHES                             │
├─────────────────────────────────────────────────────────┤
│ batch_id (PK)          NUMBER                          │
│ medication_id (FK)     NUMBER                          │
│ batch_number           VARCHAR2(50)                    │
│ expiry_date            DATE                            │
│ qty_received           NUMBER                          │
│ qty_on_hand            NUMBER                          │
│ purchase_price         NUMBER(10,2)                    │
│                                                        │
│ Constraints:                                           │
│ - PK: batch_id                                         │
│ - FK: medication_id → MEDICATIONS                      │
│ - CHECK: expiry_date > SYSDATE                        │
│ - CHECK: qty_received > 0                             │
│ - CHECK: qty_on_hand >= 0                             │
│ - CHECK: qty_on_hand <= qty_received                  │
└─────────────────────────────────────────────────────────┘
            │ 1
            │
            │
            │ N
            ▼
┌─────────────────────────────────────────────────────────┐
│                  DISPENSE_LOG                          │
├─────────────────────────────────────────────────────────┤
│ dispense_id (PK)       NUMBER                          │
│ prescription_id (FK)   NUMBER                          │
│ line_id (FK)           NUMBER                          │
│ batch_id (FK)          NUMBER                          │
│ qty_dispensed          NUMBER                          │
│ dispensed_by           VARCHAR2(100)                   │
│ dispensed_at           DATE                            │
│ override_flag          CHAR(1)                         │
│ override_reason        VARCHAR2(500)                   │
│                                                        │
│ Constraints:                                           │
│ - PK: dispense_id                                      │
│ - FK: prescription_id → PRESCRIPTIONS                 │
│ - FK: line_id → PRESCRIPTION_LINES                    │
│ - FK: batch_id → BATCHES                              │
│ - CHECK: qty_dispensed > 0                            │
│ - CHECK: override_flag IN ('Y','N')                   │
│ - DEFAULT: dispensed_at = SYSDATE                     │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│              DRUG_INTERACTIONS                         │
├─────────────────────────────────────────────────────────┤
│ interaction_id (PK)    NUMBER                          │
│ med1_id (FK)           NUMBER                          │
│ med2_id (FK)           NUMBER                          │
│ severity               VARCHAR2(20)                    │
│ description            VARCHAR2(500)                   │
│ recommended_action     VARCHAR2(500)                   │
│                                                        │
│ Constraints:                                           │
│ - PK: interaction_id                                   │
│ - FK: med1_id → MEDICATIONS                           │
│ - FK: med2_id → MEDICATIONS                           │
│ - CHECK: med1_id != med2_id                           │
│ - CHECK: severity IN ('LOW','MODERATE','HIGH',         │
│                      'CONTRAINDICATED')               │
└─────────────────────────────────────────────────────────┘
```
**Final Improvements:**

- All transitive dependencies removed
    
- Each non-key attribute depends only on the primary key
    
- All tables in 3NF
    

---

## **DATA DICTIONARY**

### **Table 1: MEDICATIONS**

**Purpose:** Master catalog of all medications available in the pharmacy.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|medication_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each medication|
|name|VARCHAR2(100)|NOT NULL|Brand/trade name of medication|
|generic_name|VARCHAR2(100)|NULL|Generic name of medication|
|requires_prescription|CHAR(1)|NOT NULL, CHECK ('Y','N')|Whether prescription is required ('Y' or 'N')|
|active_status|CHAR(1)|NOT NULL, DEFAULT 'A', CHECK ('A','I')|Active status ('A'ctive or 'I'nactive)|
|created_date|DATE|NOT NULL, DEFAULT SYSDATE|Date when medication was added|

**Primary Key:** medication_id  
**Foreign Keys:** None  
**Indexes:** Unique index on name + generic_name combination

### **Table 2: BATCHES**

**Purpose:** Tracks inventory at batch level with expiry dates for FIFO management.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|batch_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each batch|
|medication_id|NUMBER|FOREIGN KEY, NOT NULL|References MEDICATIONS(medication_id)|
|batch_number|VARCHAR2(50)|NOT NULL|Manufacturer batch/lot number|
|expiry_date|DATE|NOT NULL|Expiration date of batch|
|qty_received|NUMBER|NOT NULL, CHECK (> 0)|Original quantity received|
|qty_on_hand|NUMBER|NOT NULL, CHECK (>= 0)|Current available quantity|
|purchase_price|NUMBER(10,2)|NOT NULL|Purchase price per unit|
|received_date|DATE|NOT NULL, DEFAULT SYSDATE|Date batch was received|
|supplier|VARCHAR2(100)|NULL|Supplier/vendor name|

**Primary Key:** batch_id  
**Foreign Keys:** medication_id REFERENCES MEDICATIONS(medication_id) ON DELETE CASCADE  
**Indexes:** Composite index on (medication_id, expiry_date) for FIFO queries

### **Table 3: PATIENTS**

**Purpose:** Stores patient information including allergies for safety checks.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|patient_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each patient|
|first_name|VARCHAR2(50)|NOT NULL|Patient's first name|
|last_name|VARCHAR2(50)|NOT NULL|Patient's last name|
|dob|DATE|NOT NULL|Date of birth|
|phone|VARCHAR2(20)|NOT NULL|Contact phone number|
|email|VARCHAR2(100)|UNIQUE, NOT NULL|Email address for notifications|
|address|VARCHAR2(200)|NULL|Physical address|
|allergies|VARCHAR2(500)|NULL|Known allergies (comma-separated)|
|emergency_contact|VARCHAR2(100)|NULL|Emergency contact details|
|registration_date|DATE|NOT NULL, DEFAULT SYSDATE|Date patient registered|

**Primary Key:** patient_id  
**Foreign Keys:** None  
**Indexes:** Unique index on email; Index on (last_name, first_name)

### **Table 4: PRESCRIPTIONS**

**Purpose:** Main prescription header with status tracking.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|prescription_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each prescription|
|patient_id|NUMBER|FOREIGN KEY, NOT NULL|References PATIENTS(patient_id)|
|prescriber_name|VARCHAR2(100)|NOT NULL|Prescribing doctor's name|
|prescriber_license|VARCHAR2(50)|NULL|Doctor's license number|
|prescription_date|DATE|NOT NULL, DEFAULT SYSDATE|Date prescription was written|
|status|VARCHAR2(20)|NOT NULL, DEFAULT 'PENDING'|Current status|
|total_lines|NUMBER(3)|DEFAULT 0|Number of medication lines|
|dispensed_date|DATE|NULL|Date fully dispensed|
|notes|VARCHAR2(500)|NULL|Additional notes/comments|

**Primary Key:** prescription_id  
**Foreign Keys:** patient_id REFERENCES PATIENTS(patient_id) ON DELETE CASCADE  
**Indexes:** Index on patient_id; Index on prescription_date; Index on status

### **Table 5: PRESCRIPTION_LINES**

**Purpose:** Individual medication lines within a prescription.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|line_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each line|
|prescription_id|NUMBER|FOREIGN KEY, NOT NULL|References PRESCRIPTIONS(prescription_id)|
|medication_id|NUMBER|FOREIGN KEY, NOT NULL|References MEDICATIONS(medication_id)|
|dosage|VARCHAR2(50)|NOT NULL|Dosage instructions (e.g., "10mg twice daily")|
|frequency|VARCHAR2(50)|NULL|Administration frequency|
|duration_days|NUMBER(5)|NULL|Treatment duration in days|
|quantity_prescribed|NUMBER|NOT NULL, CHECK (> 0)|Total quantity prescribed|
|quantity_dispensed|NUMBER|NOT NULL, CHECK (>= 0)|Quantity dispensed so far|
|instructions|VARCHAR2(500)|NULL|Special instructions|

**Primary Key:** line_id  
**Foreign Keys:**

- prescription_id REFERENCES PRESCRIPTIONS(prescription_id) ON DELETE CASCADE
    
- medication_id REFERENCES MEDICATIONS(medication_id)  
    **Indexes:** Composite index on (prescription_id, medication_id)
    

### **Table 6: DRUG_INTERACTIONS**

**Purpose:** Configurable safety rules for drug interaction checking.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|interaction_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each interaction rule|
|med1_id|NUMBER|FOREIGN KEY, NOT NULL|References MEDICATIONS(medication_id)|
|med2_id|NUMBER|FOREIGN KEY, NOT NULL|References MEDICATIONS(medication_id)|
|severity|VARCHAR2(20)|NOT NULL|Severity level of interaction|
|description|VARCHAR2(500)|NOT NULL|Description of interaction|
|recommended_action|VARCHAR2(500)|NULL|Recommended action if detected|
|created_date|DATE|NOT NULL, DEFAULT SYSDATE|When rule was created|
|active_flag|CHAR(1)|NOT NULL, DEFAULT 'Y', CHECK ('Y','N')|Whether rule is active|

**Primary Key:** interaction_id  
**Foreign Keys:**

- med1_id REFERENCES MEDICATIONS(medication_id)
    
- med2_id REFERENCES MEDICATIONS(medication_id)  
    **Indexes:** Composite index on (med1_id, med2_id); Index on severity
    

### **Table 7: DISPENSE_LOG**

**Purpose:** Complete audit trail of all dispensing transactions (FACT TABLE for BI).

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|dispense_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique identifier for each dispense|
|prescription_id|NUMBER|FOREIGN KEY, NOT NULL|References PRESCRIPTIONS(prescription_id)|
|line_id|NUMBER|FOREIGN KEY, NOT NULL|References PRESCRIPTION_LINES(line_id)|
|batch_id|NUMBER|FOREIGN KEY, NOT NULL|References BATCHES(batch_id)|
|qty_dispensed|NUMBER|NOT NULL, CHECK (> 0)|Quantity dispensed in this transaction|
|dispensed_by|VARCHAR2(100)|NOT NULL|Pharmacist who dispensed|
|dispensed_at|DATE|NOT NULL, DEFAULT SYSDATE|Date/time of dispensing|
|override_flag|CHAR(1)|NOT NULL, DEFAULT 'N', CHECK ('Y','N')|Whether safety check was overridden|
|override_reason|VARCHAR2(500)|NULL|Reason for override (if any)|

**Primary Key:** dispense_id  
**Foreign Keys:**

- prescription_id REFERENCES PRESCRIPTIONS(prescription_id)
    
- line_id REFERENCES PRESCRIPTION_LINES(line_id)
    
- batch_id REFERENCES BATCHES(batch_id)  
    **Indexes:**
    
- Index on prescription_id for prescription history
    
- Index on batch_id for batch tracking
    
- Index on dispensed_at for time-based analytics
    
- Index on override_flag for compliance reporting
    

### **Table 8: AUDIT_LOG**

**Purpose:** Generic audit trail for all table changes (Type 4 SCD).

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|log_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique audit log entry|
|table_name|VARCHAR2(50)|NOT NULL|Name of table being modified|
|operation|VARCHAR2(10)|NOT NULL, CHECK ('INSERT','UPDATE','DELETE')|Type of operation|
|record_id|VARCHAR2(100)|NOT NULL|ID of affected record|
|old_values|CLOB|NULL|Previous values (JSON format)|
|new_values|CLOB|NULL|New values (JSON format)|
|changed_by|VARCHAR2(100)|NOT NULL|User who made change|
|change_time|DATE|NOT NULL, DEFAULT SYSDATE|When change occurred|
|ip_address|VARCHAR2(50)|NULL|User's IP address|
|session_id|VARCHAR2(100)|NULL|Database session ID|

**Primary Key:** log_id  
**Foreign Keys:** None  
**Indexes:** Composite index on (table_name, change_time); Index on changed_by

### **Table 9: STOCK_ALERTS**

**Purpose:** Tracks all system-generated alerts for inventory issues.

|Column Name|Data Type|Constraints|Description|
|---|---|---|---|
|alert_id|NUMBER|PRIMARY KEY, NOT NULL, AUTO_INCREMENT|Unique alert identifier|
|medication_id|NUMBER|FOREIGN KEY, NOT NULL|References MEDICATIONS(medication_id)|
|alert_type|VARCHAR2(20)|NOT NULL|Type of alert|
|qty_on_hand|NUMBER|NULL|Current stock level (for low stock alerts)|
|expiry_date|DATE|NULL|Expiry date (for expiry alerts)|
|alert_time|DATE|NOT NULL, DEFAULT SYSDATE|When alert was generated|
|resolved_flag|CHAR(1)|NOT NULL, DEFAULT 'N', CHECK ('Y','N')|Whether alert is resolved|
|resolved_by|VARCHAR2(100)|NULL|Who resolved the alert|
|resolved_time|DATE|NULL|When alert was resolved|
|notes|VARCHAR2(500)|NULL|Resolution notes|

**Primary Key:** alert_id  
**Foreign Keys:** medication_id REFERENCES MEDICATIONS(medication_id)  
**Indexes:** Index on medication_id; Index on alert_time; Index on resolved_flag

---

## **KEY ASSUMPTIONS**

### **Business Rules**

1. **Minimum Age:** Patients must be at least 13 years old to comply with data protection regulations (GDPR, HIPAA).
    
2. **Email Uniqueness:** Each patient must have a unique email address for identification and communication.
    
3. **Batch Expiry:** All batches must have future expiry dates (cannot add expired medication).
    
4. **Prescription Status Flow:** Prescriptions follow workflow: PENDING → APPROVED → DISPENSED or CANCELLED/EXPIRED.
    
5. **FIFO Enforcement:** When dispensing, system always allocates from the oldest batch (earliest expiry date) first.
    
6. **Override Requirements:** Any safety check override requires a mandatory reason for audit trail.
    
7. **Interaction Severity Levels:** Only four severity levels: LOW, MODERATE, HIGH, CONTRAINDICATED.
    

### **Data Integrity**

1. **Cascading Deletes:**
    
    - Delete patient → Delete their prescriptions → Delete prescription lines
        
    - Delete medication → Delete associated batches and interactions
        
    - Delete prescription → Delete associated dispense logs
        
2. **No Orphaned Records:** Foreign key constraints prevent:
    
    - Dispense logs without valid prescriptions
        
    - Batches without valid medications
        
    - Prescription lines without valid prescriptions or medications
        
3. **Quantity Validation:**
    
    - Cannot dispense more than prescribed quantity
        
    - Cannot have negative inventory
        
    - Cannot dispense more than available in batch
        
4. **Automatic Timestamps:**
    
    - dispensed_at, alert_time, change_time default to SYSDATE
        
    - prescription_date defaults to current date
        

### **System Behavior**

1. **Atomic Safety Checks:** Drug interaction and allergy checks are performed atomically within the database transaction.
    
2. **Automatic Alert Generation:** System generates alerts daily for:
    
    - Low stock (below minimum threshold)
        
    - Near expiry (within 30 days)
        
    - Expired batches (past expiry date)
        
3. **Automatic Audit Logging:** All DML operations on critical tables are logged via triggers.
    
4. **Mandatory Override Capture:** When pharmacist overrides safety check, system requires and logs the reason.
    
5. **Batch-Level Allocation:** Inventory management happens at batch level, not medication level.
    

### **Future Extensibility**

1. **Allergy Structure:** Current design uses comma-separated text. Could be normalized to PATIENT_ALLERGIES table.
    
2. **Multiple Suppliers:** Current design has single supplier per batch. Could extend to SUPPLIERS table.
    
3. **Pricing Structure:** Current design has single purchase price. Could add selling_price and price_history.
    
4. **Enhanced Reporting:** Current audit trail supports basic compliance. Could add specialized reporting tables.
    

---

## **BI CONSIDERATIONS**

### **Fact vs. Dimension Tables**

#### **Fact Tables (Measures & Events):**

1. **DISPENSE_LOG - Central fact table**
    
    - **Measures:** qty_dispensed (quantitative data)
        
    - **Time Dimension:** dispensed_at (temporal analysis)
        
    - **Foreign Keys:** Links to all dimension tables
        
    - **Grain:** One row per dispensing transaction
        
    - **Aggregations Supported:**
        
        - SUM(qty_dispensed) - Total units dispensed
            
        - COUNT(*) - Number of transactions
            
        - COUNT(DISTINCT prescription_id) - Unique prescriptions
            
        - AVG(qty_dispensed) - Average dispense quantity
            
2. **PRESCRIPTIONS - Secondary fact table**
    
    - **Measures:** total_lines (count of medications)
        
    - **Time Dimension:** prescription_date
        
    - **Status Dimension:** status (workflow tracking)
        
3. **STOCK_ALERTS - Monitoring fact table**
    
    - **Measures:** qty_on_hand (for low stock alerts)
        
    - **Time Dimension:** alert_time
        
    - **Event Type:** alert_type
        

#### **Dimension Tables (Descriptive Attributes):**

1. **PATIENTS - "Who" dimension**
    
    - Provides patient context (demographics, allergies)
        
    - Supports filtering by patient attributes
        
    - Used for patient-specific reporting
        
2. **MEDICATIONS - "What" dimension (Level 1)**
    
    - Provides medication context (name, type, prescription requirement)
        
    - Supports filtering by medication attributes
        
    - Used for medication analysis
        
3. **BATCHES - "What" dimension (Level 2) + "When" dimension**
    
    - Provides batch context (supplier, expiry, price)
        
    - Time dimension: expiry_date for shelf-life analysis
        
    - Used for inventory and cost analysis
        
4. **PRESCRIPTION_LINES - "What" dimension (Detail)**
    
    - Provides prescription detail context (dosage, quantity)
        
    - Bridges prescriptions to medications
        
    - Used for dosage pattern analysis
        
5. **DRUG_INTERACTIONS - "Safety" dimension**
    
    - Provides safety rule context
        
    - Used for interaction frequency analysis
        
    - Supports safety compliance reporting
        

### **Star Schema Representation**

text

        PATIENTS (Dimension)
             |
             | patient_id
             |
             ▼
    PRESCRIPTIONS (Fact) ◄── Contains: total_lines, status
             |
             | prescription_id
             |
             ▼
    PRESCRIPTION_LINES (Dimension)
    |                  |
    | medication_id    | prescription_id
    ▼                  ▼
MEDICATIONS (Dimension)   DISPENSE_LOG (Fact) ◄── Contains: qty_dispensed, override_flag
    |                  |                  |
    | medication_id    | batch_id         | line_id
    ▼                  ▼                  ▼
DRUG_INTERACTIONS    BATCHES (Dimension)   (recursive link)
(Dimension)               |
                      medication_id
                          |
                          ▼
                    MEDICATIONS (Dimension)

### **Slowly Changing Dimensions (SCD)**

#### **Type 1 SCD: Overwrite (No History)**

|Dimension|Attribute|Strategy|Rationale|
|---|---|---|---|
|PATIENTS|phone, email, address|Type 1 - Overwrite|Contact info updates should reflect current info|
|MEDICATIONS|generic_name|Type 1 - Overwrite|Generic name corrections; historical not needed|
|BATCHES|qty_on_hand|Type 1 - Overwrite|Current quantity is what matters|

**Implementation:** Direct UPDATE statements

#### **Type 2 SCD: Add New Row (Track History)**

|Dimension|Attribute|Strategy|Rationale|
|---|---|---|---|
|MEDICATIONS|active_status|Type 2 - Add row|Track when medications become inactive|
|DRUG_INTERACTIONS|severity, recommended_action|Type 2 - Add row|Track changes to safety rules|
|PATIENTS|allergies|Type 2 - Add row|Critical for audit; track allergy changes|

**Implementation:** Add effective_date, expiry_date, is_current columns

#### **Type 4 SCD: Separate History Table (Implemented)**

|Dimension|Attribute|Strategy|Rationale|
|---|---|---|---|
|ALL TABLES|All changes|Type 4 - AUDIT_LOG|Complete change history for compliance|
|DISPENSE_LOG|override_reason|Type 4 - Built-in|Already has timestamp and user|

**Implementation:** AUDIT_LOG table with old_values and new_values

### **Aggregation Levels**

#### **By Patient (Individual Analysis)**

- Total medications dispensed per patient
    
- Average prescription value
    
- Allergy patterns and interaction history
    
- Compliance rate (prescriptions filled vs prescribed)
    

#### **By Medication (Product Analysis)**

- Dispensing volume and frequency
    
- Stock turnover rate
    
- Expiry waste percentage
    
- Popularity ranking
    

#### **By Batch (Inventory Analysis)**

- Shelf-life utilization
    
- Supplier performance
    
- Cost analysis per batch
    
- Expiry patterns by supplier
    

#### **By Time Period (Trend Analysis)**

- Daily/weekly/monthly dispensing volumes
    
- Prescription trends over time
    
- Seasonal patterns
    
- Growth metrics
    

#### **By Safety (Compliance Analysis)**

- Interaction detection frequency
    
- Override rates and reasons
    
- Allergy incident tracking
    
- Safety compliance metrics
    

#### **Cross-Dimensional Analysis**

- Patient × Medication: Which patients use which medications
    
- Medication × Time: Seasonal demand patterns
    
- Batch × Supplier: Quality and expiry analysis
    
- Safety × Pharmacist: Override patterns by pharmacist
    

### **Audit Trail Design**

#### **Existing Audit Capabilities:**

1. **DISPENSE_LOG:** Complete dispensing audit
    
    - What was dispensed, when, by whom
        
    - Batch allocation details
        
    - Override information
        
2. **AUDIT_LOG:** Generic change tracking
    
    - All DML operations on all tables
        
    - Before/after values
        
    - User and timestamp
        
3. **Built-in Audit Fields:**
    
    - dispensed_at, alert_time, change_time
        
    - dispensed_by, changed_by
        
    - override_flag, override_reason
        

#### **Future Enhancements:**

1. **Business Process Audit:** Track prescription workflow state changes
    
2. **User Session Audit:** Complete user activity tracking
    
3. **Data Access Audit:** Track SELECT operations on sensitive data
    
4. **Compliance Reporting:** Pre-built reports for regulatory requirements
    

---

## **CONCLUSION**

This logical model design achieves **3NF normalization** while specifically supporting the Smart Pharmacy Management System's core requirements:

1. **Batch-Level FIFO Management:** BATCHES table with expiry_date enables proper inventory rotation
    
2. **Drug Safety Checking:** DRUG_INTERACTIONS table with PATIENTS.allergies enables comprehensive safety validation
    
3. **Complete Audit Trail:** DISPENSE_LOG and AUDIT_LOG provide regulatory compliance tracking
    
4. **Alert Generation:** STOCK_ALERTS table supports proactive inventory management
    
5. **BI-Ready Structure:** Clear fact/dimension separation enables comprehensive analytics
    
6. **Scalable Design:** Partitioning-ready structure for high-volume pharmacy operations
    

The model successfully transforms the project requirements into a robust, normalized database schema that supports both transactional processing and business intelligence while maintaining data integrity and supporting regulatory compliance.
