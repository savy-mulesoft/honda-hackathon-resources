# Honda Mule Vibe Hackathon – Plan & Expectations

---

## 🎯 Goal

Before hackathon day, every participant must already be **environment-ready** so the live session can focus on **AI-assisted API build and implementation**, not setup issues.

By the end of pre-work, each participant should be able to:

1. Run the starter Mule app locally (`main`)
2. Publish an API spec to Anypoint Exchange using Mule Vibe Agent (`main`)
3. Connect Mule to DB2 and perform read/write against a pre-created table (`advanced_db2`)

---

## 🌿 Mandatory Branches to Complete

**Repo:** [https://github.com/savy-mulesoft/honda-mule-vibe-starter](https://github.com/savy-mulesoft/honda-mule-vibe-starter)

**Required:**

* `main`         (basic branch to validate connectivity to exchange, build and local deployment)
* `advanced_db2` (advanced branch to validate connectivity to DB2)

**Not required:**

* `advanced` (validates SQL Server connectivity)

---

## 📦 Pre-Hackathon Deliverable (Readiness Proof)

Each participant must share a short **“done” update** confirming readiness.

* **Main branch:**

  * `curl http://localhost:8081/api/hello` returns success
  * `/welcome` added via Mule Vibe Agent and **published to Exchange**

* **advanced_db2:**

  * DB2 config updated in `application.properties`
  * Table created for participant employee ID
  * Successful GET/PUT calls with DB2 read/write working

---

## 🗄️ DB2 Setup Standard (Required)

<details>
<summary><strong>Click to expand DB2 setup details</strong></summary>

### Schema Isolation

Create a dedicated schema for hackathon work to avoid collisions. Refer initial_setup.sql (link below)

### One Table per Participant

Use one table per participant, e.g. `prdt_ctgry_<employeeId>`. Refer initial_setup.sql (link below)

### SQL Execution

* Open: `advanced_db2/src/main/resources/sql/initial_setup.sql`(https://github.com/savy-mulesoft/honda-mule-vibe-starter/blob/advanced_db2/src/main/resources/sql/initial_setup.sql)
* Repeat for each employee, replace placeholder table name with employee ID
* execute on DB2

### Permissions (DDL vs DML)

If DDL is restricted:

* DB team should pre-create schema and participant tables
* Participant credentials must allow DML (insert/read/update)

</details>

---

## <h3>🚀 Hackathon Day Flow (LIVE SESSION) </h3>

> **Date:** Tuesday, February 3, 2026  
> **Time:** 10:00 AM – 2:00 PM EST

**Working mode:**

* Start from a clean directory
* Build from scratch using Mule Vibe Agent
* Use the provided specification as the source of truth

**Expected outcome:**

1. Working API generated from the specification
2. API spec published to Anypoint Exchange
3. DB2 persistence integrated (read/write)

---

## 🔎 Hackathon Readiness Check

<details>
<summary><strong>Click to expand readiness steps</strong></summary>

### Folder Structure

```text
honda-hackathon-0226/
└── resources/
└── product-categories-api/
```

### Specification Document

* File: `Honda Hackathon Specification.pdf`(https://github.com/savy-mulesoft/honda-hackathon-resources/blob/main/Honda%20Hackathon%20Specification.pdf)
* Download and Send to Location: `honda-hackathon-0226/resources/`

### Development Guardrails

* File: `honda-development-guardrails.md`(https://github.com/savy-mulesoft/honda-hackathon-resources/blob/main/honda-development-guardrails.md)
* Apply in Mule Dev Agent → Manage Agentforce Rules & Workflows → Global Rules → New Rule file ...  →Give name and create the file (+) > Copy/Paste the file contents.
* Replace Org ID with your Anypoint Org ID


    <img width="426" height="205" alt="image" src="https://github.com/user-attachments/assets/98e0e84d-9f72-4c9a-919e-e42eea4f1c86" />
    <img width="662" height="709" alt="Screenshot 2026-01-23 at 11 49 01 AM" src="https://github.com/user-attachments/assets/47d74834-d76b-4919-b11f-a9b648b4333c" />

    

### Prompt Guide (In Progress)

* File: `hackathon_prompt_guide.md`(https://github.com/savy-mulesoft/honda-hackathon-resources/blob/main/hackathon_promt_guide.md)
* To be referenced during hackathon

</details>

---

## ✅ Success Criteria

The hackathon is successful if:

* ✅ Everyone completes **`main` + `advanced_db2`** pre-work
* ✅ On hackathon day, the team can generate, implement, publish, and validate the API end-to-end without setup delays
