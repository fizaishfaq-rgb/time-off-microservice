# Test Cases - Time-Off Microservice

## 1. Leave Request Tests

### Test Case 1: Valid Leave Request

Input:

* employeeId = 123
* local balance = 10
* request = 2 days

Action:

* User sends POST /leave/request
* System creates request with status = pending
* System checks local balance
* System validates with HCM

Expected:

* Request status = approved
* Updated balance = 8
* HCM is treated as source of truth
* HCM and local DB are in sync
* API returns success response (200)

---

### Test Case 2: Insufficient Balance

Input:

* employeeId = 123
* local balance = 1
* request = 2 days

Action:

* User sends POST /leave/request
* System checks local balance

Expected:

* Request status = rejected
* No change in balance
* API returns error response (400)

---

### Test Case 3: Zero Balance

Input:

* employeeId = 123
* local balance = 0

Action:

* User sends POST /leave/request
* System checks local balance

Expected:

* Request status = rejected
* No change in balance
* API returns error response (400)

---

## 2. HCM Integration Tests

### Test Case 4: HCM Success Response

Input:

* Valid API call to HCM

Action:

* System calls HCM for validation

Expected:

* Balance validated successfully
* Request proceeds normally
* API returns success response (200)

---

### Test Case 5: HCM Failure

Input:

* HCM API is down

Action:

* User sends POST /leave/request
* System creates request with status = pending
* System attempts to validate with HCM

Expected:

* Retry mechanism triggered
* Request status remains pending
* API returns temporary failure response (503)

---

### Test Case 6: HCM Returns Mismatched Data

Input:

* Local balance = 10
* HCM balance = 5

Action:

* System compares local and HCM balances

Expected:

* HCM is treated as source of truth
* Local balance updated to 5
* System syncs with HCM
* API reflects updated balance

---

## 3. Sync Tests

### Test Case 7: Batch Sync Update

Input:

* HCM sends updated balances

Action:

* System processes batch data

Expected:

* Local database updated correctly
* All employee balances reflect HCM data

---

### Test Case 8: Conflict Resolution

Input:

* Different balances in HCM and local

Action:

* System resolves conflict using timestamps

Expected:

* Latest timestamp value is used
* Data remains consistent across systems

---

## 4. Idempotency Tests

### Test Case 9: Duplicate Request

Input:

* requestId = REQ123
* Same requestId sent twice

Action:

* System receives duplicate request

Expected:

* First request processed
* Second request ignored
* No duplicate records created
* System remains idempotent

---

## 5. Edge Case Tests

### Test Case 10: Negative Leave Request

Input:

* Request = -2 days

Action:

* User sends invalid request

Expected:

* Request rejected
* Error returned
* API returns validation error (400)

---

### Test Case 11: Invalid Employee

Input:

* employeeId does not exist

Action:

* System attempts to process request

Expected:

* Error returned
* Request not processed
* API returns error response (404)

---

### Test Case 12: Large Leave Request

Input:

* Request = 1000 days

Action:

* User sends excessive request

Expected:

* Request rejected
* System prevents invalid operation
* API returns error response (400)

---

## 6. Concurrency Test

### Test Case 13: Concurrent Requests

Input:

* employeeId = 123
* balance = 5
* Two requests of 3 days sent simultaneously

Action:

* System processes both requests concurrently

Expected:

* Only one request approved
* Second request rejected due to insufficient balance
* No negative balance occurs
* No race condition occurs
* Data remains consistent

---

## 7. Test Coverage Summary

This test suite covers:

* Core functionality (leave requests)
* HCM integration (success, failure, mismatch)
* Data synchronization (batch updates, conflicts)
* Idempotency (duplicate prevention)
* Edge cases (invalid inputs, extreme values)
* Concurrency handling (simultaneous requests)

The goal is to ensure system reliability, data consistency, and robustness against failures.
