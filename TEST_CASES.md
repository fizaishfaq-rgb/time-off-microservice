# Test Cases - Time-Off Microservice

## 1. Leave Request Tests

### Test Case 1: Valid Leave Request

Input:

* employeeId = 123
* local balance = 10
* request = 2 days

Action:

* User sends POST /leave/request
* System checks local balance
* System validates with HCM

Expected:

* Request status = approved
* Updated balance = 8
* HCM and local DB are in sync

---

### Test Case 2: Insufficient Balance

Input:

* employeeId = 123
* local balance = 1
* request = 2 days

Action:

* User sends POST /leave/request

Expected:

* Request status = rejected
* No change in balance

---

### Test Case 3: Zero Balance

Input:

* employeeId = 123
* local balance = 0

Action:

* User sends POST /leave/request

Expected:

* Request rejected
* No change in balance

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

---

### Test Case 5: HCM Failure

Input:

* HCM API is down

Action:

* System attempts to validate request

Expected:

* Retry mechanism triggered
* Request status remains pending

---

### Test Case 6: HCM Returns Mismatched Data

Input:

* Local balance = 10
* HCM balance = 5

Action:

* System compares both balances

Expected:

* Local balance updated to 5
* System syncs with HCM

---

## 3. Sync Tests

### Test Case 7: Batch Sync Update

Input:

* HCM sends updated balances

Action:

* System processes batch data

Expected:

* Local database updated correctly

---

### Test Case 8: Conflict Resolution

Input:

* Different balances in HCM and local

Action:

* System resolves conflict

Expected:

* Latest timestamp value is used
* Data remains consistent

---

## 4. Idempotency Tests

### Test Case 9: Duplicate Request

Input:

* Same requestId sent twice

Action:

* System receives duplicate request

Expected:

* First request processed
* Second request ignored

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

---

### Test Case 11: Invalid Employee

Input:

* employeeId does not exist

Action:

* System attempts to process request

Expected:

* Error returned
* Request not processed

---

### Test Case 12: Large Leave Request

Input:

* Request = 1000 days

Action:

* User sends excessive request

Expected:

* Request rejected
* System prevents invalid operation

---

## 6. Test Coverage Summary

This test suite covers:

* Core functionality (leave requests)
* HCM integration (success, failure, mismatch)
* Data synchronization (batch updates, conflicts)
* Idempotency (duplicate prevention)
* Edge cases (invalid inputs, extreme values)

The goal is to ensure system reliability, data consistency, and robustness against failures.
