# Test Cases - Time-Off Microservice

## 1. Leave Request Tests

### Test Case 1: Valid Leave Request

* Input: employee has 10 days, requests 2 days
* Expected: Request approved, balance becomes 8

---

### Test Case 2: Insufficient Balance

* Input: employee has 1 day, requests 2 days
* Expected: Request rejected

---

### Test Case 3: Zero Balance

* Input: employee has 0 days
* Expected: Request rejected

---

## 2. HCM Integration Tests

### Test Case 4: HCM Success Response

* Input: Valid API call to HCM
* Expected: Balance validated successfully

---

### Test Case 5: HCM Failure

* Input: HCM API is down
* Expected: Retry triggered, request remains pending

---

### Test Case 6: HCM Returns Wrong Data

* Input: Local balance = 10, HCM returns 5
* Expected: Local data updated to 5

---

## 3. Sync Tests

### Test Case 7: Batch Sync Update

* Input: HCM sends updated balances
* Expected: Local database updated correctly

---

### Test Case 8: Conflict Resolution

* Input: Different balances in HCM and local
* Expected: Latest timestamp wins

---

## 4. Idempotency Tests

### Test Case 9: Duplicate Request

* Input: Same requestId sent twice
* Expected: Second request ignored

---

## 5. Edge Case Tests

### Test Case 10: Negative Leave Request

* Input: Request for -2 days
* Expected: Rejected

---

### Test Case 11: Invalid Employee

* Input: employeeId does not exist
* Expected: Error returned

---

### Test Case 12: Large Leave Request

* Input: Request for very high number (1000 days)
* Expected: Rejected
