# Mock HCM API Design

## Overview

Since the real HCM system is external, we simulate its behavior using mock APIs. These APIs help test integration, validation, synchronization, and failure scenarios.

---

## 1. Get Employee Balance

Endpoint:
GET /mock-hcm/balance

Query Params:

* employeeId
* locationId

Response (200):
{
"employeeId": "123",
"locationId": "L1",
"balance": 10
}

Error Responses:

* 404 → Employee not found
* 503 → HCM service unavailable

---

## 2. Update Balance (Time-Off Deduction)

Endpoint:
POST /mock-hcm/update

Request Body:
{
"employeeId": "123",
"locationId": "L1",
"days": 2
}

Response:

* 200 → Balance updated successfully
* 400 → Insufficient balance
* 404 → Employee not found
* 503 → HCM service unavailable

---

## 3. Batch Balance Sync

Endpoint:
POST /mock-hcm/batch

Response (200):
[
{
"employeeId": "123",
"locationId": "L1",
"balance": 8,
"lastUpdated": "2026-01-01T10:00:00Z"
},
{
"employeeId": "456",
"locationId": "L2",
"balance": 15,
"lastUpdated": "2026-01-01T10:05:00Z"
}
]

---

## 4. Error Simulation

To test failure scenarios, mock APIs can simulate:

* API downtime → return 503
* Invalid employee → return 404
* Insufficient balance → return 400
* Delayed response → simulate timeout

---

## 5. Purpose

These mock APIs allow:

* Testing real-time validation with HCM
* Simulating failures and retries
* Verifying data synchronization logic
* Ensuring system robustness without real HCM dependency
