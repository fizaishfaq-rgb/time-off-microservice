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

### Table: EmployeeBalance

* employeeId (string)
* locationId (string)
* balance (number)
* lastUpdated (timestamp)

### Table: LeaveRequest

* requestId (string)
* employeeId (string)
* locationId (string)
* days (number)
* status (pending / approved / rejected)
* createdAt (timestamp)

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

---

## 10. Detailed API Design

### 1. Request Leave

POST /leave/request

Request Body:

```
{
  "employeeId": "123",
  "locationId": "L1",
  "days": 2
}
```

Response:

* Success: Leave requested successfully
* Error: Insufficient balance

---

### 2. Get Balance

GET /leave/balance?employeeId=123&locationId=L1

Response:

```
{
  "balance": 10
}
```

---

### 3. Approve Leave

POST /leave/approve

Request Body:

```
{
  "requestId": "REQ123"
}
```

Response:

* Success: Leave approved

---

### 4. Sync with HCM

POST /sync/hcm

Response:

* Success: Data synced

---

## 11. System Flow

1. Employee sends leave request
2. Microservice checks local balance
3. Microservice calls HCM API to validate
4. If valid → approve request
5. If invalid → reject request
6. Update balance in both systems

---

## 12. Data Consistency Strategy

* Every leave request will first be validated against the HCM system
* Local database will act as a temporary cache but not the source of truth
* In case of mismatch, HCM data will override local data
* Periodic sync will reconcile differences

---

## 13. Request Lifecycle

* Pending → Initial state when request is created
* Approved → After validation and manager approval
* Rejected → If insufficient balance or validation fails

---

## 14. Failure Handling Strategy

* If HCM API fails:

  * Retry mechanism will be triggered
  * Request will remain in "pending" state

* If HCM returns inconsistent data:

  * System will log discrepancy
  * Trigger sync process

---

## 15. Idempotency Handling

* Each request will have a unique requestId
* Duplicate requests with same requestId will be ignored

---

## 16. Batch Sync Handling

* System will accept bulk updates from HCM
* Local balances will be updated accordingly
* Conflicts will be resolved using latest timestamp



