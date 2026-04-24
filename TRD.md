# Time-Off Microservice - Technical Requirement Document (TRD)

## 1. Problem Statement

Employees request time off using ReadyOn, but the actual leave balance is stored in an external HCM system. Keeping both systems synchronized is challenging and can lead to incorrect leave approvals or mismatched balances.

---

## 2. Goals

* Ensure accurate leave balance for employees
* Validate leave requests in real-time
* Synchronize data with HCM system
* Prevent invalid or inconsistent data

---

## 3. Challenges

* Multiple systems updating HCM
* Real-time API dependency
* Batch updates from HCM
* Possible API failures or incorrect responses

---

## 4. System Overview

The Time-Off Microservice will act as an intermediary between ReadyOn and HCM. It will handle leave requests, validate balances, and synchronize data.

---

## 5. API Design

### Endpoints:

* POST /leave/request → Request leave
* GET /leave/balance → Get leave balance
* POST /leave/approve → Approve leave
* POST /sync/hcm → Sync data from HCM

---

## 6. Database Design

Table: EmployeeBalance

* employeeId
* locationId
* balance

---

## 7. Sync Strategy

* Real-time validation using HCM API
* Periodic batch updates from HCM

---

## 8. Edge Cases

* HCM API failure
* Insufficient balance
* Duplicate requests
* Data mismatch

---

## 9. Alternatives Considered

* Polling vs Webhooks
* Caching vs real-time validation
  
