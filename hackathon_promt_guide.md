
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
Remove the variable-based mock persistence, Connect to DB2 database and persist the data in the database instead.
Use Generic Connection with IBM DB2 JDBC driver. Include proper shared library configuration, connection pooling, and basic CRUD operations.
Create placegolder for DB2 connection details application.properties, including the schema name and table name.
