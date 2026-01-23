
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


Goal:
1) Create the API RAML for the Product Categories API exactly as defined in the specification.
2) Publish the API (RAML) to my Anypoint Exchange.
3) Implement the API in this project.

Phase 1 implementation constraint:
- Do NOT connect to DB2 yet.
- Mock persistence using local variables:
  - PUT: store the incoming payload into a variable (temporary in-memory store).
  - GET: retrieve and return the value from that variable.
- Ensure GET and PUT behavior matches the specification (request/response shape, status codes, errors if applicable).

After completing the implementation:
- Create a test document named `test_api.md` that includes:
  - How to run the app locally
  - curl command for PUT (with a sample payload)
  - curl command for GET
  - Expected responses for both
