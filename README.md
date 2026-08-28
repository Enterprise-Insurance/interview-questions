# Technical Exercise: Funeral Claim Filing Module

## Context

Enterprise Transitions handles funeral policy claims. When a policyholder
passes away, a beneficiary needs to file a claim so the company can take
over funeral costs. Build a small module for this.

**Time budget:** ~35–40 minutes
**Language:** PHP/Laravel, Python, or JS/Node — whichever you're strongest in

## Provided

A starter repo with 5 sample policies (seed data below — JSON or SQLite,
your choice). No other boilerplate; set up your own server/routes.

## Instructions

### 1. Setup (~5 min)

- `git init` (or use the provided repo). Commit as you go — aim for
  meaningful incremental commits, not one dump at the end.
- Load the seed data into SQLite or keep it as JSON.

### 2. Policy lookup (~10 min)

- Build `GET /api/policy/:policyNumber` — returns the matching policy as
  JSON, or a clear 404 if not found.
- On the HTML form: a "Policy Number" field + Search button. On search,
  call this endpoint and prefill read-only fields: full name, policy type,
  coverage amount, status.

### 3. Claim submission (~15 min)

- Once a policy is found, show fields for: claimant name, relationship to
  deceased, date of death, cause of death (free text), estimated funeral
  cost.
- Build `POST /api/claims` that validates:
  - the policy number exists
  - date of death is not in the future
  - required fields aren't empty
- Save the claim to its own table/collection, tagged `status: "pending"`
  and a timestamp.

### 4. List claims (~5 min)

- `GET /api/claims` — all submitted claims, most recent first.

### Bonus (only if time remains)

- `PATCH /api/claims/:id` to set status to `approved`/`rejected`. Do it on
  a separate branch, merge back.

## Deliverable

Working code + git log — be ready to walk through your commits and
explain your validation decisions.

---

## Seed data — `policies.json`

```json
[
  { "policy_number": "FN-1001", "full_name": "Ama Owusu", "policy_type": "Family Cover", "coverage_amount": 5000, "start_date": "2022-03-01", "status": "active" },
  { "policy_number": "FN-1002", "full_name": "Kojo Mensah", "policy_type": "Individual Cover", "coverage_amount": 3000, "start_date": "2021-07-15", "status": "active" },
  { "policy_number": "FN-1003", "full_name": "Efua Asante", "policy_type": "Family Cover", "coverage_amount": 5000, "start_date": "2020-01-10", "status": "lapsed" },
  { "policy_number": "FN-1004", "full_name": "Yaw Boateng", "policy_type": "Individual Cover", "coverage_amount": 2500, "start_date": "2023-05-20", "status": "active" },
  { "policy_number": "FN-1005", "full_name": "Abena Darko", "policy_type": "Family Cover", "coverage_amount": 6000, "start_date": "2019-11-02", "status": "active" }
]
```

### Equivalent SQLite schema (if you prefer a DB)

```sql
CREATE TABLE policies (
  policy_number TEXT PRIMARY KEY,
  full_name TEXT NOT NULL,
  policy_type TEXT NOT NULL,
  coverage_amount INTEGER NOT NULL,
  start_date TEXT NOT NULL,
  status TEXT NOT NULL
);

CREATE TABLE claims (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  policy_number TEXT NOT NULL,
  claimant_name TEXT NOT NULL,
  relationship TEXT NOT NULL,
  date_of_death TEXT NOT NULL,
  cause_of_death TEXT,
  estimated_cost INTEGER,
  status TEXT DEFAULT 'pending',
  submitted_at TEXT DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (policy_number) REFERENCES policies(policy_number)
);
```
