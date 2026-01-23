# Honda Mule Vibe Hackathon - Plan & Expectations


## Goal

Before hackathon day, every participant should already be able to:

1.  **Run the starter Mule app locally** (Main branch)
2.  **Publish an API spec to Anypoint Exchange using Mule Vibe Agent** (Main branch)
3.  **Connect Mule to DB2 and perform read/write against a pre-created table** (advanced\_db2)

This confirms the environment is ready, and the team can focus on **AI-assisted API build + implementation** on hackathon day (not Maven/JDK/Exchange connectivity).

## What branches to complete (mandatory)

Repo: <https://github.com/savy-mulesoft/honda-mule-vibe-starter>

✅ **Required**

  * `main`
  * `advanced_db2`

🚫 **Not required**

  * `advanced` (which focuses on SQL Server Connectivity)

## Pre-hackathon deliverable: “Readiness Proof” (what each participant must share)

Each participant to share a short “done” update with:

### A) Main branch proof (local run + Exchange publish)

  * Screenshot (or copy/paste) of:
      * `curl http://localhost:8081/api/hello` returning a success response
  * Proof that `/welcome` was added via Mule Vibe Agent and **published to Exchange** (screenshot of Exchange asset / API Console, or the agent output confirmation).

### B) `advanced_db2` proof (DB2 connectivity)

  * Confirmation they updated DB2 settings in `application.properties` (host/port/db/user/schema/table).
  * Proof they executed the DB2 setup SQL and have a table ready for their employee ID (see next section).
  * Proof they can run:
      * `GET`/`PUT` calls for product-categories endpoints (or whatever endpoints your `advanced_db2` exposes) and DB2 read/write works.

-----

## DB2 setup standard (important for hackathon smoothness)

### 1\) Schema isolation (recommended)

Create a dedicated schema for hackathon work so it doesn’t collide with anything else (example schema mentioned in the repo docs).

### 2\) One table per employee (strongly recommended)

To avoid collisions on hackathon day, create **one table per participant** (e.g., `prdt_ctgry_<employeeId>`). This matches the repo’s guidance to parameterize the table name by employee id.

### 3\) SQL execution

Participants should:

  * Open: `advanced_db2/src/main/resources/sql/initial_setup.sql`
      * Path: [advanced\_db2/src/main/resources/sql/initial\_setup.sql](https://github.com/savy-mulesoft/honda-mule-vibe-starter/blob/advanced_db2/src/main/resources/sql/initial_setup.sql)
  * Replace the placeholder table name (example shown as `prdt_ctgry_12345`) with their own ID, and ensure schema name matches.
  * Execute it on DB2.

### 4\) Permissions note (DDL vs DML)

During the dry run, Honda team flagged they may **not** have rights to create tables themselves (DDL), but can likely do inserts/reads (DML) using a service-style credential.

**Action:** If DDL is restricted, have the DB team pre-create:

  * the schema
  * all participant tables

...and ensure participants have credentials that allow DML on those tables.

-----
# <mark> <h3>🚀 Hackathon day flow (what we will do live) </h3></mark>
> **Date:** Tuesday, February 3, 2026  
> **Time:** 10:00 AM – 2:00 PM EST
---

### Working mode

We start from a **clean/blank directory** and build from scratch with Mule Vibe Agent using the provided spec document (`Honda Hackathon Specification.pdf`).

### Expected outcome by end of hackathon

Participants (with Mulesoft SE guiding) will produce:

1.  **A working API project generated from the provided specification** (API + implementation)
2.  **Published API spec to Anypoint Exchange** (so it’s discoverable/testable via Exchange)
3.  **DB2 persistence integrated** (write + read) — assuming permissions and connectivity are already validated from `advanced_db2`

## What we will send you a few days before hackathon

1.  **A curated list of prompts** you’ll run live with them (so everyone’s aligned)
2.  A **guardrail file (`rules_guardrail.md`)** + instructions for applying it in Anypoint Code Builder

## Success criteria (simple “green check” definition)

We call the hackathon a success if:

✅ Everyone completed **Main +** **`advanced_db2`** pre-work (local run + Exchange publish + DB2 connectivity).
✅ On hackathon day, the team can generate + implement an API from the spec, publish it, and validate end-to-end behavior without spending time on setup.
