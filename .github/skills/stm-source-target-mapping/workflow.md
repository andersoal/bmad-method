# Source-to-Target Mapping Workflow

**Goal:** Produce a usable source-to-target mapping artifact that translates nested source structures into target schema fields with explicit rules, defaults, and business meaning.

**Use this for:** API integration, ETL design, payload-to-table mapping, JSON/XML flattening, migration planning, and auditing transformation logic.

## Working Rules

- Prefer concrete source and target artifacts over abstract discussion.
- If the user provides sample payloads but no formal schema, infer the mapping carefully and mark assumptions.
- If required inputs are missing, ask only for the minimum blocking details.
- Treat arrays, optional fields, enums, dates, decimals, and null/default handling as first-class mapping concerns.
- Always surface transformation risks, not just the happy-path mapping.

## Inputs To Gather

Collect these inputs when available:

1. Source structure
   - JSON, XML, CSV, or object model
   - Schema, sample payload, or both
   - Event-level or record-level grain

2. Target structure
   - Table name, schema, or API contract
   - Target column names and data types
   - Nullability and primary/foreign key constraints when known

3. Mapping behavior
   - Direct copy, lookup, format, calculate, split, merge, flatten, aggregate
   - Default value behavior for null or missing source fields
   - Required business rules and validation rules

4. Delivery format
   - Markdown table by default
   - Add CSV-style output in a code block if the user asks for spreadsheet import

## Output Structure

Produce the artifact with these sections in order:

### 1. Context Summary

- Integration or migration name
- Source system and target system
- Grain of the mapping
- Assumptions that affect mapping correctness

### 2. Source-to-Target Mapping Table

Use the template in `./templates/source-target-mapping-template.md`.

Required columns:

| STM Component | Purpose |
| --- | --- |
| Source Field Path | Full path to the source field, including nesting or XPath-like path |
| Source Data Type | Native source type or inferred type |
| Transformation Rule | Rule used to derive the target value |
| Target Column Name | Destination field or column |
| Target Data Type | Database or target-contract type |
| Default Value | Fallback when source value is missing or invalid |
| Business Definition | Semantic meaning of the mapped field |
| Example of Data | Example source and or target value |

### 3. Transformation Notes

Add short notes only for non-trivial logic such as:

- Array flattening or row explosion
- Code-to-description lookups
- Unit conversion
- Date parsing and timezone normalization
- Precision or scale handling
- Composite key generation

### 4. Risks And Open Questions

List only the unresolved items that could cause implementation defects, such as:

- Missing source field definitions
- Ambiguous array handling
- Type mismatches
- Nullability conflicts
- Missing default behavior
- Business-rule ambiguity

## Path Conventions

- For JSON, use dotted paths and index placeholders where helpful, for example `customer.address.postalCode` or `order.items[*].sku`.
- For XML, use stable element paths, for example `/Order/Customer/PostalCode`.
- If the source can repeat, prefer `[*]` instead of a numeric index unless a specific ordinal is required.

## Transformation Rule Guidance

Use short, auditable rule phrases. Prefer these patterns:

- `Direct` for straight passthrough
- `Format: yyyy-MM-dd -> yyyyMMdd`
- `Lookup: status_code -> status_name`
- `Calculate: quantity * unitPrice`
- `Concatenate: firstName + ' ' + lastName`
- `Split: fullName -> firstName,lastName`
- `Flatten: order.items[*].sku`
- `Conditional: if missing then default 'UNKNOWN'`

## Minimum Quality Bar

Before finishing, verify that:

- Every target column has a source or an explicit default/derived rule.
- Every non-direct transformation is explained in plain language.
- Data types are compatible or the mismatch is called out.
- Null and missing behavior is documented.
- At least one example value is included for each row when sample data exists.

## Delivery Notes

- When the user wants a reusable artifact, save the result under `docs/data-mapping/` or another user-specified path.
- When the user is still designing, keep the output in chat first and ask before creating project documents.