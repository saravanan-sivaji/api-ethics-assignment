# API Ethics and Data Privacy Assignment

## Task 1 — Classify and Handle PII Fields


| Field | Classification | Action | Justification |
| :--- | :--- | :--- | :--- |
| **full_name** | Direct PII | **Drop** | Explicitly identifies an individual; high risk with low research value. |
| **email** | Direct PII | **Drop** | Unique contact identifier; unnecessary for health trend analysis. |
| **date_of_birth** | Indirect PII | **Mask** | Can lead to re-identification; convert to Age or Birth Year. |
| **zip_code** | Indirect PII | **Mask** | Precise locations are identifying; truncate to first 3 digits. |
| **job_title** | Indirect PII | **Pseudonymize** | Specific titles can identify; replace with broad industry categories. |
| **diagnosis_notes** | Indirect PII | **Mask** | May contain "hidden" PII; scrub identifiers or use ICD-10 codes. |

## Task 2 — Audit the API Script for Ethical Compliance

### Violation 1: Excessive Data Collection (Data Minimization)
**Problem:** The original script stores raw records including direct identifiers (`full_name`, `email`) permanently. This violates the principle of storing only the minimum data necessary.

**Corrected Code:**
```python
cleaned_records = []
for record in data["results"]:
    # Remove direct identifiers immediately
    del record["full_name"]
    del record["email"]
    # Transform DOB to Age to preserve privacy
    record["age"] = calculate_age(record["date_of_birth"])
    cleaned_records.append(record)

save_to_database(cleaned_records)

### Violation 2: Lack of Rate Limiting (TOS Violation)
***Problem:** Making 100 consecutive requests as fast as possible on a "free tier" key can overwhelm the API and violate Terms of Service.
```python
import time

for page in range(1, 101):
    response = requests.get(API_URL, params={"page": page, "key": API_KEY})
    
    if response.status_code == 200:
        data = response.json()
        # ... process data ...
    
    # Respect the API's rate limits by pausing for 1 second
    time.sleep(1) 
