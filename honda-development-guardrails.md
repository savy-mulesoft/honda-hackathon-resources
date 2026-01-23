# MuleSoft Development Rules - Streamlined

---

## 🔧 Configuration Constants

> **⚠️ UPDATE THESE VALUES BEFORE USE** - All templates and examples reference these constants

| Constant | Value | Usage |
|----------|-------|-------|
| **Organization ID** | `e5c02810-ef86-427e-8e6b-f3d3abe55974` | POM groupId, Exchange publishing, exchange.organization.id |
| **Mule Runtime** | `4.10.0` | app.runtime, minMuleVersion |
| **Java Version** | `17` | maven.compiler.source/target, javaSpecificationVersions |
| **Mule Maven Plugin** | `4.5.2` | mule.maven.plugin.version |

---

## Template Essentials

### mule-artifact.json Template

```json
{
  "minMuleVersion": "{MULE_RUNTIME}",
  "javaSpecificationVersions": ["{JAVA_VERSION}"]
}
```

### POM Template Key Elements

```xml
<groupId>{ORG_ID}</groupId>
<app.runtime>{MULE_RUNTIME}</app.runtime>
<maven.compiler.source>{JAVA_VERSION}</maven.compiler.source>
<maven.compiler.target>{JAVA_VERSION}</maven.compiler.target>
<exchange.organization.id>{ORG_ID}</exchange.organization.id>
<mule.maven.plugin.version>{MULE_MAVEN_PLUGIN_VERSION}</mule.maven.plugin.version>
```

---

## Project Type Decision Tree

### 🔄 Determine Project Type First

**API Project**: Building REST/GraphQL APIs that expose endpoints for consumption
- Keywords: "API", "REST API", "endpoints", "OpenAPI", "RAML", "expose data"
- **Workflow**: Spec-First → Publish → Implement with APIKit Router - everything within same project unless explicitly specified to be in different projects

**Integration Project**: Connecting systems, data transformation, batch processing  
- Keywords: "integration", "connect", "sync", "migrate", "transform", "ETL"
- **Workflow**: Direct Implementation → Connect systems

---

## API Development Workflow (Spec-First Approach)

### 🔄 Critical APIKit Configuration Decision Point

**MANDATORY: ALWAYS start with Local Development approach first**

**Phase 1: Local Development (ALWAYS START HERE)**
- ✅ **Required first step** - Must be working before any Exchange publishing
- ✅ **Immediate validation** - Ensures specification and flows work correctly
- ✅ **Build validation** - Guarantees `mvn clean package` success
- ⚠️ **Never skip this step** - Exchange publishing without local validation leads to failures

**Phase 2: Exchange Publishing (Only after local success)**
- ✅ **Production ready** - Follows MuleSoft best practices after validation
- ✅ **API governance** - Centralized specification management
- ⚠️ **Prerequisites required** - Local development must be successful first

### Step-by-Step Process for APIs

#### 🚀 Phase 1: Create and Test Locally (Always Start Here)

1. **Create API Specification**
   - Create RAML or OpenAPI specification file in `src/main/resources/`
   - Define endpoints, data types, examples, and responses
   - Include proper validation rules and documentation

2. **Configure APIKit for Local Development**
   - ✅ **MANDATORY: Use local file reference first**: Reference specification file directly from resources folder
   - ❌ **NEVER start with Exchange reference**: Exchange references will fail if asset not published
   - ⚠️ **Critical Pattern**: APIKit config must reference local file for immediate testing and validation
   - 🔄 **Quality Gate**: Application must start successfully with local reference before proceeding

3. **Implement with APIKit Router**
   - Create flows with APIKit naming convention: `get:\path\(param):config-name`
   - APIKit handles routing, validation, and error responses automatically
   - Test locally first to validate specification and flows

4. **Flow Implementation**
   - Implement business logic in operation-specific flows
   - Use DataWeave for response generation
   - Include logging and correlation IDs

#### 🏭 Phase 2: Promote to Exchange (When Ready for Production)

5. **Publish to Anypoint Exchange** (Optional - only when ready for production)
   - Use `create_and_manage_assets` tool to publish API spec
   - Set classifier as "raml" or "oas" 
   - This makes the spec available for APIKit Router reference

6. **Update for Exchange Reference** (Optional - only after publishing)
   - Add published Exchange asset as POM dependency:
   ```xml
   <dependency>
     <groupId>{ORG_ID}</groupId>
     <artifactId>api-name</artifactId>
     <version>1.0.0</version>
     <classifier>raml</classifier>
     <type>zip</type>
   </dependency>
   ```
   - Update APIKit config to reference Exchange asset:
   ```xml
   <apikit:config api="resource::{ORG_ID}:asset-id:version:raml:zip:spec.raml" .../>
   ```

7. **In-Memory Storage Standards (CRITICAL)**

   #### Default Approach (MANDATORY)
   - **ALWAYS use flow variables** for in-memory storage by default
   - Simple, compatible with all Java versions
   - No additional dependencies required
   - **This is the DEFAULT - use variables unless explicitly told otherwise**

   #### Alternative Approaches (EXCEPTION ONLY - Explicit Request Required)
   - **ObjectStore**: Only when user explicitly requests "ObjectStore" or "object store"
   - **H2 Database**: Only when user explicitly requests "H2" or "embedded database"
   - **External Database**: Only when user specifies database connection details

   > **Rule**: Default to flow variables. Use alternatives ONLY when explicitly requested by user.

### ⚠️ APIKit Router Error Prevention

**Root Cause Analysis: APIKit Router Issues**
- **Primary Cause**: Using Exchange resource references before publishing to Exchange
- **Secondary Cause**: Missing POM dependencies for Exchange references
- **Tertiary Cause**: Incorrect Maven artifact configurations

**MANDATORY APIKit Configuration Sequence:**
1. **Phase 1 - Local Development**: Always configure with local file reference first
2. **Phase 2 - Build Validation**: Ensure `mvn clean package` succeeds with local reference
3. **Phase 3 - Exchange Publishing**: Only after local success, publish specification to Exchange
4. **Phase 4 - Exchange Integration**: Update POM dependencies and APIKit config for Exchange reference

**Generic Error Resolution Pattern:**
- **Symptom**: APIKit router startup failures or RAML not found errors
- **Solution**: Revert APIKit config to local file reference pattern
- **Validation**: Run build command to ensure successful compilation and startup

---

## Integration Workflow (Direct Implementation)

### CRITICAL: Pre-Setup

- Check if project directory exists, use different name if needed
- **Always run `mkdir -p project-name` before creating files** (macOS)
- Ensure final build succeeds AND XML schema validates (no red errors)

### Step-by-Step Process for Integrations

1. **Parse Requirements**
   - Source system → Target system
   - Data flow direction (uni/bidirectional, batch/real-time)
   - Transformation needs

2. **Select Connectors** (Follow Dynamic Documentation Approach)
   - Go to [MuleSoft Connectors Documentation](https://docs.mulesoft.com/connectors/)
   - Find the connector by name (e.g., "Apache Kafka", "Slack", "Salesforce")
   - Click on the connector to go to latest version documentation
   - **Verify Java Compatibility**: Ensure connector version supports **{JAVA_VERSION}** (per configuration constants)
   - Navigate to "Examples" section for official implementation patterns
   - Use the examples to get correct:
     - Maven dependency information (compatible with Java version)
     - XML namespace and schema declarations
     - Configuration patterns
     - Operation usage examples
   - If connector not found → Search Anypoint Exchange
   - If still not found → Ask user: REST/HTTP, JDBC, File, or Custom

3. **Create Project Structure**
   ```
   project-name/
   ├── pom.xml (use template base)
   ├── mule-artifact.json
   └── src/main/
       ├── mule/project-flow.xml
       └── resources/application.properties
   ```

4. **Add Dependencies**
   - Get connector dependencies from official documentation examples
   - Required JDBC drivers for databases, if required
   - Keep minimal and up-to-date
   - **⚠️ CRITICAL: JDBC Driver Shared Library Configuration**
     - **MANDATORY**: All JDBC drivers MUST be configured as shared libraries in Mule Maven plugin
     - **Failure Pattern**: Without shared library config, drivers cause `Cannot load class` classloader errors
     - **Required Configuration**: Add `<sharedLibraries>` section to mule-maven-plugin in POM
     - **Common JDBC Drivers**: SQL Server, MySQL, PostgreSQL, Oracle - ALL require shared library configuration

5. **Build Flow (Integration-Specific)**
   - Trigger: HTTP Listener, Scheduler, or Message Source (Kafka, JMS)
   - Source connector operation
   - Transform (DataWeave)
   - Target connector operation
   - Logging (correlation IDs, entry/exit points)
   - **Error handling is OPTIONAL** - only add if specifically requested

6. **Quality Gates** ✅
   - **CRITICAL: Maven Build Success**: `mvn clean package` MUST succeed - retry implementation until successful
   - **CRITICAL: XML Schema Validation**: No red errors in IDE - fix all schema validation issues
   - **Version Consistency**: POM runtime = mule-artifact.json minMuleVersion (both must be **{MULE_RUNTIME}**)
   - **Java Configuration**: Verify both files contain **{JAVA_VERSION}** settings:
     - POM.xml: `<maven.compiler.source>{JAVA_VERSION}</maven.compiler.source>` and `<maven.compiler.target>{JAVA_VERSION}</maven.compiler.target>`
     - mule-artifact.json: `"javaSpecificationVersions": ["{JAVA_VERSION}"]`
   - **Mule Runtime**: Use **{MULE_RUNTIME}** with `<app.runtime>{MULE_RUNTIME}</app.runtime>`
   - **Canvas UI Compatibility**: Flows render properly in Anypoint Studio canvas (no red errors)
   - **Build Retry Protocol**: If build fails, analyze errors and retry implementation until `mvn clean package` succeeds

---

## Dynamic Connector Documentation Workflow

### 🔄 Always Use Latest Official Documentation

Instead of static connector information, follow this dynamic approach:

1. **Start at Connectors Hub**: https://docs.mulesoft.com/connectors/
2. **Find Your Connector**: Search or browse for the connector (e.g., "Apache Kafka", "Slack")
3. **Go to Latest Version**: Click the connector link to access `/latest/` documentation
4. **Navigate to Examples**: Look for "Examples" in the navigation menu
5. **Extract Information**: From the examples, get:
   - Correct Maven dependency with latest version
   - Proper XML namespaces and schema locations
   - Configuration patterns and best practices
   - Operation usage examples

### 📋 Information to Extract from Examples

For each connector, gather:
- **Maven Dependency**: `groupId:artifactId:version:classifier`
- **XML Namespace**: `xmlns:prefix="namespace-url"`
- **Schema Location**: Full schema URL for `xsi:schemaLocation`
- **Configuration Pattern**: How to set up the connector config
- **Operation Examples**: Correct syntax for operations
- **Error Types**: Valid error types for error handling (from "Error Types" or "Error Handling" section)
- **Best Practices**: Any connector-specific rules or patterns

### ⚠️ Why This Approach is Better

- **Always Current**: Gets latest versions and patterns
- **Official Examples**: Uses MuleSoft's verified examples
- **Complete Context**: Includes full configuration context
- **Best Practices**: Incorporates official recommendations
- **Java Compatible**: Ensures compatibility with **{JAVA_VERSION}** runtime

---

## Core Connectors (Always Required)

### Core Mule Runtime

| Component | Value |
|-----------|-------|
| **Namespace** | `xmlns="http://www.mulesoft.org/schema/mule/core"` |
| **Schema** | `http://www.mulesoft.org/schema/mule/core http://www.mulesoft.org/schema/mule/core/current/mule.xsd` |

### Documentation Namespace

| Component | Value |
|-----------|-------|
| **Namespace** | `xmlns:doc="http://www.mulesoft.org/schema/mule/documentation"` |

### Enterprise Edition Core

| Component | Value |
|-----------|-------|
| **Namespace** | `xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core"` |
| **Schema** | `http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd` |

---

## Quick Decision Trees

### API Development Flow
```
API Request → Create RAML/OAS Spec → Configure APIKit Router (Local Reference) → Implement Operation Flows
    ↓
Test Locally → Validate
    ↓
Quality Gates: Build Success + Schema Valid + APIKit Working → Deploy/Test
    ↓
Optional: Publish to Exchange → Update POM → Update APIKit Config (Exchange Reference) → Production Ready
```

### Integration Development Flow  
```
Integration Request → Find Connectors (docs.mulesoft.com) → Extract Dependencies
    ↓
Create Project → Add Dependencies → Build Flow → Validate
    ↓
Quality Gates: Build Success + Schema Valid + Version Match → Deploy/Test
```

---

## Essential Best Practices

### API Projects
- **Naming**: `api-name-api` (e.g., `vehicle-api`, `customer-api`)
- **Spec-First**: Always create specification first, start with local development
- **APIKit Router**: Start with local file reference, optionally promote to Exchange later
- **Flow Naming**: Follow APIKit convention `method:\path\(params):config`
- **Console**: Include API console flow for testing

### Integration Projects  
- **Naming**: `source-target-integration` (lowercase-hyphen)
- **Direct Implementation**: No APIKit needed, build flows directly
- **Connector Focus**: Emphasize connector configuration and operations

### Validation Best Practices ⚠️

**XML Schema Compliance for Input Validation:**
- ✅ **Use choice-based validation** for simple header/field checks (RECOMMENDED):
  ```xml
  <choice doc:name="Validate Required Headers">
      <when expression="#[isEmpty(attributes.headers.consumerId) or isEmpty(attributes.headers.consumerName)]">
          <raise-error type="CUSTOM:MISSING_HEADER" description="Required headers missing" />
      </when>
      <otherwise>
          <logger level="DEBUG" doc:name="Headers Valid" message="All headers present" />
      </otherwise>
  </choice>
  ```

- ⚠️ **Validation module elements** (`validation:is-not-empty`, etc.) require specific XML structure patterns and can cause schema validation errors
- 🔄 **Always test Maven build** (`mvn clean compile`) after adding ANY validation logic
- 📋 **Prefer standard Mule flow elements** (choice/when/raise-error) over specialized validation modules for simple checks

**Quality Gate for Validation:** Ensure `mvn clean compile` succeeds after implementing validation logic

### Common Practices
- **Security**: Externalize credentials, use properties
- **Logging**: Include correlation IDs, log entry/exit points
- **Error Handling**: OPTIONAL - only add if specifically requested by user
- **Performance**: Use batch for large data, implement pagination
- **XML Validation**: Always include `xmlns:doc` namespace and proper schema locations
- **Logger Levels**: Use hardcoded values (INFO, ERROR, DEBUG) instead of properties for validation
- **Documentation First**: Always check official examples before implementing

### Error Handling Best Practices ⚠️

**Documentation-First Error Type Validation:**
- ✅ **Always verify error types** against official connector documentation before implementation
- 🔍 **Check Error Handling Section**: Go to `https://docs.mulesoft.com/[connector-name]/latest/` → Navigate to "Error Handling" or "Error Types" section
- ❌ **Never assume error type names** - they vary between connectors and versions
- 📋 **Extract from official docs**: Use only error types listed in current documentation

**Common Database Connector Error Types (Example):**
- ✅ `DB:CONNECTIVITY` - Connection issues
- ✅ `DB:QUERY_EXECUTION` - Query execution problems
- ✅ `DB:BAD_SQL_SYNTAX` - SQL syntax errors
- ✅ `DB:QUERY_TIMEOUT` - Query timeout issues
- ❌ `DB:SQL_QUERY_EXECUTION` - **INVALID - Does not exist**

**Error Type Validation Process:**
1. **Before adding error handlers**: Check official connector documentation
2. **Reference current docs**: Use `/latest/` version documentation
3. **Validate build**: Run `mvn clean compile` after adding error handlers
4. **Fix invalid types**: Remove or correct any compilation errors related to error types

**Quality Gate Addition:** All error types must be validated against official documentation before implementation

---

## Build Validation and Error Recovery Protocol

### 🚨 CRITICAL: Build Success Requirement

**MANDATORY BUILD VALIDATION:**
- **Primary Rule**: `mvn clean package` MUST succeed before task completion
- **Secondary Rule**: If build fails, analyze and retry implementation until successful
- **Tertiary Rule**: Never present incomplete solution with build errors

**Build Failure Recovery Protocol:**
1. **Immediate Action**: Run `mvn clean package` after any code changes
2. **Error Analysis**: Identify root cause of compilation/build failures
3. **Systematic Fix**: Address each error category systematically
4. **Retry Implementation**: Modify code and retry build until successful
5. **Quality Validation**: Ensure XML schema validation passes (no red errors)

**Common Build Error Categories:**
- **Compilation Errors**: Missing dependencies, syntax errors, incorrect imports
- **Schema Validation**: XML namespace issues, invalid element structures
- **Runtime Configuration**: Version mismatches, invalid property references
- **Dependency Conflicts**: Version incompatibilities, missing artifacts

---

## DataWeave Error Prevention Guidelines

### 🔄 Generic DataWeave Best Practices

**Null Safety and Type Coercion:**
- **Always use null-safe operators**: Use `orElse` for default values instead of assuming non-null
- **Avoid direct type casting**: Use conditional logic for type conversions
- **Handle empty collections**: Check for empty arrays/objects before processing
- **Use defensive programming**: Validate data structure before transformation

**Common Error Patterns to Avoid:**
- **Null pointer exceptions**: Always check for null before accessing properties
- **Type casting errors**: Use safe type conversion methods
- **Array access errors**: Validate array length before index access
- **String coercion issues**: Use explicit string conversion functions

**DataWeave Version Compatibility:**
- **Use supported syntax**: Avoid lambda-style try syntax in older DataWeave versions
- **Prefer explicit error handling**: Use if-else conditions over try-catch patterns
- **Test transformations**: Validate DataWeave expressions in DataWeave playground

**Safe Transformation Patterns:**
- **Conditional Access**: Use `field if (condition) else defaultValue`
- **Null Checks**: Use `payload.field default "defaultValue"`
- **Type Validation**: Check type before casting or conversion
- **Error Boundaries**: Wrap complex transformations in conditional logic

---

## SQL Query and Database Best Practices

### 🔄 Generic SQL Error Prevention

**Parameter Handling:**
- **Filter null parameters**: Only include non-null parameters in query construction
- **Dynamic WHERE clauses**: Generate WHERE conditions based on provided parameters only
- **Safe parameter binding**: Validate parameters before SQL binding
- **Default values**: Use appropriate defaults for optional parameters

**Query Construction Patterns:**
- **Conditional SQL building**: Construct queries based on available parameters
- **Parameter validation**: Check parameter presence before including in query
- **SQL injection prevention**: Use parameterized queries for all dynamic values
- **Error handling**: Implement proper database error handling patterns

**Database Connection Management:**
- **Connection validation**: Test database connectivity before operations
- **Transaction management**: Use appropriate transaction boundaries
- **Resource cleanup**: Ensure proper connection and resource disposal
- **Timeout configuration**: Set appropriate query timeout values
- **SSL Configuration**: For cloud/demo databases, add connection properties to handle SSL certificate validation
- **Common SSL Properties**: `encrypt=true`, `trustServerCertificate=true` for bypassing certificate validation issues

---

## Generic Error Handling Patterns

### 🔄 Common Error Types and Prevention

**Runtime Errors:**
- **Null Reference Errors**: Always validate object existence before property access
- **Type Conversion Errors**: Use safe type conversion methods with fallbacks
- **Collection Access Errors**: Check collection size before index operations
- **Configuration Errors**: Validate configuration properties at startup

**Integration Errors:**
- **Connectivity Issues**: Implement connection retry logic and timeouts
- **Data Format Errors**: Validate data structure before processing
- **Transformation Errors**: Use defensive programming in DataWeave expressions
- **Protocol Errors**: Handle communication protocol failures gracefully

**Error Recovery Strategies:**
- **Graceful Degradation**: Provide fallback mechanisms for non-critical failures
- **Retry Logic**: Implement appropriate retry patterns for transient errors
- **Circuit Breaker**: Use circuit breaker pattern for external service calls
- **Dead Letter Queue**: Route failed messages to error handling flows

---

## Try-Catch and Exception Handling Guidelines

### 🔄 Generic Exception Handling Patterns

**DataWeave Transform Error Handling:**
- **Avoid lambda-style try syntax**: Use conditional expressions instead
- **Use if-else patterns**: Replace try-catch with conditional logic
- **Validate before transform**: Check data structure before transformation
- **Provide default values**: Use `orElse` and `default` operators

**Flow-Level Error Handling:**
- **Use error handlers sparingly**: Only add when specifically required
- **Validate error types**: Check official documentation for valid error types
- **Implement proper logging**: Include correlation IDs and error context
- **Choose appropriate error scopes**: Use flow-level or global error handlers

**Transform-Specific Error Prevention:**
- **Pre-validation**: Validate input structure before complex transformations
- **Safe operators**: Use null-safe navigation and default values
- **Type checking**: Verify data types before casting operations
- **Boundary conditions**: Handle edge cases in data transformations

---

## DataWeave & Transformation Resources

### 📚 Primary Documentation
| Resource | Link |
|----------|------|
| DataWeave Overview | https://docs.mulesoft.com/dataweave/latest/ |
| Language Guide | https://docs.mulesoft.com/dataweave/latest/dataweave-language-guide |
| Functions Reference | https://docs.mulesoft.com/dataweave/latest/dw-functions |

### 🎓 Learning Resources
| Resource | Link |
|----------|------|
| Quickstart Guide | https://docs.mulesoft.com/dataweave/latest/dataweave-quickstart |
| Examples | https://docs.mulesoft.com/dataweave/latest/dataweave-examples |

### 🔄 DataWeave Development Guidelines

#### Core Best Practices
- **Always use null-safe operators**: Use `default` and `orElse` for handling null values
- **Avoid complex nested expressions**: Break down complex transformations into smaller, readable parts
- **Use proper variable declarations**: Declare variables in the header section using `var variableName = value`
- **Follow functional programming principles**: Use immutable data structures and pure functions
- **Test transformations**: Validate DataWeave expressions using the DataWeave playground or Transform Message component preview

#### Syntax Guidelines
- **Variable Declaration**: Use `var` keyword in header section, not within expressions
- **Conditional Logic**: Use `if-else` statements instead of complex ternary operations where readability matters
- **Object Construction**: Use proper object syntax `{ key: value }` with appropriate spacing
- **Array Operations**: Leverage built-in functions like `map`, `filter`, `reduce` for array manipulations
- **Error Handling**: Use defensive programming with null checks and default values

#### Functional Programming in DataWeave
- **Pure Functions**: DataWeave functions always return the same output for the same input
- **Immutable Data**: Variables cannot change their value after assignment
- **Function Composition**: Chain functions to build complex transformations
- **Lambda Expressions**: Use anonymous functions for operations like `map`, `filter`, `reduce`
- **No Side Effects**: Functions don't modify external state, only return values

#### Performance Guidelines
- **Use lazy evaluation**: DataWeave evaluates expressions only when needed
- **Optimize large dataset processing**: Consider streaming for very large payloads
- **Cache expensive operations**: Store results of complex calculations in variables
- **Minimize nested iterations**: Use efficient DataWeave functions instead of nested loops

#### Transformation Error Prevention
- **Validate input structure**: Check for required fields before accessing them
- **Handle missing data**: Use `default` operators for optional fields
- **Type safety**: Be explicit about data types when necessary
- **Test edge cases**: Validate transformations with empty, null, and malformed data
- **Avoid `do` blocks**: Use simple conditional expressions and header variables instead
- **Use header variables**: Declare all variables in the header section, not within the body

#### Common DataWeave Patterns

```dataweave
# ✅ CORRECT: Header with variable declarations
%dw 2.0
output application/json
var inputData = payload.data default []
var config = p('app.config')
---
# Body with transformation logic
{
  results: inputData map (item, index) -> {
    id: item.id,
    name: item.name default "Unknown",
    processed: true
  }
}
```

```dataweave
# ❌ AVOID: Do blocks with variable declarations
%dw 2.0
output application/json
---
do {
  var inputData = payload.data
  ---
  { results: inputData }
}
```

#### DataWeave Syntax Error Prevention
- **Never use `do` blocks for variable declarations**: Always declare variables in header
- **Avoid semicolons in variable declarations**: DataWeave doesn't require semicolons
- **Use proper conditional syntax**: Stick to `if-else` patterns, avoid complex `do` constructs
- **Test syntax in playground**: Validate DataWeave expressions before implementation
- **Follow official documentation patterns**: Always reference latest MuleSoft examples

---

## Java Compatibility Guidelines

### 🚨 CRITICAL: Java Module Compatibility Issues

> **Remember**: Java **{JAVA_VERSION}** is the mandatory version per Configuration Constants

**MANDATORY Java Compatibility Checks:**
- **Extension 'Validation' does not support Java {JAVA_VERSION}** - Supported versions: [1.8, 11]
- **Extension 'Scripting' does not support Java {JAVA_VERSION}** - Supported versions: [1.8, 11]
- ⚠️ **NEVER downgrade Java version** - Java {JAVA_VERSION} is mandatory per template requirements

### ✅ Java {JAVA_VERSION} Compatible Alternatives

**Replace Validation Module with Choice-Based Validation:**
```xml
<!-- ✅ RECOMMENDED: Use choice-based validation instead -->
<choice doc:name="Validate Required Input">
    <when expression="#[payload == null or isEmpty(payload)]">
        <raise-error type="CUSTOM:INVALID_INPUT" description="Input is required"/>
    </when>
    <otherwise>
        <logger level="DEBUG" doc:name="Input Valid" message="Input validation passed"/>
    </otherwise>
</choice>
```

**Replace Scripting Module with DataWeave Transformations:**
```xml
<!-- ❌ AVOID: Java scripting (not Java {JAVA_VERSION} compatible) -->
<scripting:execute engine="java">
    <scripting:code><![CDATA[
        // Java code here
    ]]></scripting:code>
</scripting:execute>

<!-- ✅ RECOMMENDED: Use DataWeave transformations -->
<ee:transform doc:name="Process Data using DataWeave">
    <ee:message>
        <ee:set-payload><![CDATA[%dw 2.0
output application/java
---
// DataWeave transformation logic here
payload map (item, index) -> {
    // transformation logic
}]]></ee:set-payload>
    </ee:message>
</ee:transform>
```

### 🔄 Error Resolution Protocol for Java Issues

**Common Error Patterns:**
- `Extension 'Validation' does not support Java {JAVA_VERSION}. Supported versions are: [1.8, 11]`
- `Extension 'Scripting' does not support Java {JAVA_VERSION}. Supported versions are: [1.8, 11]`
- `JavaVersionNotSupportedByExtensionException`

**Resolution Steps:**
1. **Identify incompatible modules** from error messages
2. **Remove module dependencies** from POM.xml
3. **Replace functionality** with Java {JAVA_VERSION} compatible alternatives:
   - Validation → Choice-based validation
   - Scripting → DataWeave transformations
4. **Remove XML namespaces** for removed modules
5. **Test build success** with `mvn clean package`

**Build Validation After Java Fixes:**
- ✅ Maven build succeeds: `mvn clean package`
- ✅ No XML schema validation errors
- ✅ Application starts without deployment exceptions
- ✅ All functionality replaced with compatible alternatives

### 📋 Java Compatibility Checklist

**Before Implementation:**
- [ ] Check all dependencies for Java {JAVA_VERSION} support
- [ ] Avoid Validation and Scripting modules
- [ ] Use DataWeave for all transformations
- [ ] Use choice-based validation patterns

**After Implementation:**
- [ ] Build succeeds with `mvn clean package`
- [ ] No Java version compatibility errors in logs
- [ ] XML schema validation passes
- [ ] All removed functionality has compatible replacements
