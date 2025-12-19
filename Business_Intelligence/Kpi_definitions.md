# KPI Definitions – Smart Pharmacy Management System

## 1. Overview

Key Performance Indicators (KPIs) are measurable metrics used to evaluate the performance, safety, efficiency, and compliance of the Smart Pharmacy Management System.  
They support data-driven decision-making for pharmacy management, pharmacists, and regulatory authorities.

---

## 2. KPIs

| KPI Name                      | Definition                                              | Calculation / Notes                                                              |
| ----------------------------- | ------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Total Active Medications      | Number of medications currently active in the system    | COUNT(\*) FROM MEDICATIONS WHERE active_status = 'Y'                             |
| Total Prescriptions Processed | Total number of prescriptions handled in a period       | COUNT(\*) FROM PRESCRIPTIONS WHERE date BETWEEN period                           |
| Dispensed Prescriptions       | Number of prescriptions successfully dispensed          | COUNT(\*) WHERE status = 'DISPENSED'                                             |
| Pending Prescriptions         | Number of prescriptions awaiting dispensing             | COUNT(\*) WHERE status = 'PENDING'                                               |
| Total Medications Dispensed   | Total quantity of medications dispensed                 | SUM(qty_dispensed) FROM DISPENSE_LOG                                             |
| FIFO Compliance Rate          | Percentage of batches dispensed in expiry-date order    | (FIFO-compliant dispenses / total dispenses) × 100                               |
| Near-Expiry Batch Count       | Number of batches close to expiry                       | COUNT(\*) FROM BATCHES WHERE expiry_date <= SYSDATE + threshold_days             |
| Expired Batch Count           | Number of expired medication batches                    | COUNT(\*) FROM BATCHES WHERE expiry_date < SYSDATE                               |
| Low Stock Medication Count    | Medications below minimum stock level                   | COUNT(\*) FROM STOCK_ALERTS WHERE alert_type = 'LOW_STOCK'                       |
| Drug Interaction Alerts       | Number of drug–drug interaction warnings generated      | COUNT(\*) FROM DRUG_INTERACTIONS JOIN DISPENSE_LOG                               |
| Allergy Alerts                | Number of allergy warnings triggered                    | COUNT(\*) FROM DISPENSE_LOG WHERE override_flag = 'N' AND allergy_detected = 'Y' |
| Dispensing Overrides          | Number of pharmacist overrides during dispensing        | COUNT(\*) FROM DISPENSE_LOG WHERE override_flag = 'Y'                            |
| Average Dispensing Time       | Average time to complete a dispensing process           | AVG(dispensed_at - prescription_date)                                            |
| Audit Log Entries             | Total number of audited database operations             | COUNT(\*) FROM AUDIT_LOG                                                         |
| Inventory Turnover Rate       | Frequency at which inventory is dispensed over a period | Total qty dispensed / Average inventory on hand                                  |

---

## 3. Notes

- All KPIs must be filterable by date range, medication, batch, or pharmacist.
- KPI values must reconcile with core transactional tables to ensure accuracy.
- Safety-related KPIs (interactions, allergies, overrides) are critical for regulatory compliance.
- Inventory KPIs support waste reduction and stock optimization.

---
