
# Honda Hackathon — Prompt Guide 

This guide contains the **curated prompts** we will run live during the hackathon to ensure everyone follows the same, repeatable flow.

1. Open **Anypoint Code Builder (VS Code)**.
2. **File → Open Folder…**
3. Select and open this folder (project root):
honda-hackathon-0226/product-categories-api
4. Once the folder is open, click on Mule dev agent
5. Ensure that the Global Rule - honda-development-guardrails.md - is turned ON 

---

## Phase 1 — Generate API from Spec + Publish to Exchange + Implement with Local Variables

Refer ../resources/Honda Hackathon Specification.pdf
Using this specification, create the Product Categories API RAML, publish the API to my Anypoint Exchange, and implement the API in this project.
For now, do not connect to any database.
Mock persistence using local variables:
- For PUT requests, store the incoming payload in a local variable.
- For GET requests, return the value from that variable.
Also generate a small test document with curl commands to validate both the PUT and GET operations.


## Phase 2 — Replace the variable-based mock with real DB2 persistence while keeping the same API contract.

Update the existing implementation.

Remove the variable-based mock persistence.
Connect to a DB2 database and persist the data in the database instead.

Use a Generic Connection with the IBM DB2 JDBC driver.
Include proper shared library configuration, connection pooling, and basic CRUD operations.

Create placeholders for DB2 connection details in application.properties, including:
- host
- port
- database name
- username
- password
- schema name
- table name
- created_by // to be replaced with user_id by the developer

Align the implementation with the following schema sql:

CREATE SCHEMA mule_hkthn_0226;

CREATE TABLE IF NOT EXISTS mule_hkthn_0226.PRDT_CTGRY (
    product_line                  VARCHAR(10)   NOT NULL,
    product_category_code         VARCHAR(50)   NOT NULL,
    product_category_group_code   VARCHAR(50)   NOT NULL,

    product_category_name         VARCHAR(200),
    door_type                     VARCHAR(20),
    cylinder_count                VARCHAR(20),
    motive_power                  VARCHAR(50),
    product_category_group_name   VARCHAR(200),

    business_key_delete_indicator CHAR(1),
    last_action_code              CHAR(1),

    created_by                    VARCHAR(50)   NOT NULL,

    created_utc TIMESTAMP NOT NULL DEFAULT CURRENT TIMESTAMP,
    updated_utc TIMESTAMP NOT NULL DEFAULT CURRENT TIMESTAMP,

    CONSTRAINT PK_PRDT_CTGRY PRIMARY KEY (
        product_line,
        product_category_code,
        product_category_group_code,
        created_by
    )
);

