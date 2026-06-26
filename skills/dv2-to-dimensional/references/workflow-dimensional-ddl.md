# Workflow 1: Data Vault Model To Dimensional DDL

Use this workflow to convert Snowflake Data Vault 2.0 DDL plus optional metadata into dimensional Snowflake DDL, a manifest, and a single-page HTML microsite.

## Inputs

Read from any combination of:

- **Live Snowflake schema**: Use `sql_execute` with `SHOW TABLES IN SCHEMA <db>.<schema>` and `SELECT GET_DDL('TABLE', '<fqn>')` to pull DDL directly.
- **Local files**: `dv2_to_dimensional/inputs/ddl/*.sql` and `dv2_to_dimensional/inputs/metadata/*`

Accept Snowflake DDL for hubs, links, satellites, PITs, bridges, effectivity satellites, reference tables, and business vault objects.

## Steps

1. **Inventory entities**: Run `SHOW TABLES IN SCHEMA` or scan local DDL files. Classify each as hub, link, satellite, effectivity satellite, PIT, bridge, reference, business vault object, or unknown.
2. **Parse key columns**: Use `DESCRIBE TABLE` or parse DDL to identify hash keys, business keys, load timestamps, record source columns, relationship keys, effective dates, and descriptive attributes.
3. **Discovery phase**: Run the interactive discovery from `discovery.md` for business process, fact grain, SCD, exclusion, and naming decisions.
4. **Apply conversion rules**: Use `conversion-rules.md` to propose dimensions, facts, bridges, factless facts, and reference/decoded attributes.
5. **Generate Snowflake DDL**: Produce dimensional DDL only. Do not generate load SQL or CTAS transformation SQL in this workflow.
6. **Generate manifest**: Write `outputs/manifests/conversion_manifest.yml`.
7. **Generate microsite**: Write `outputs/docs/index.html`.
8. **Optional deployment**: If the user requests, execute the generated DDL via `sql_execute` to create tables in Snowflake.

## DDL Requirements

- Use `CREATE OR REPLACE TABLE`.
- Use Snowflake-compatible data types.
- Use `COMMENT` clauses where useful.
- Document primary and foreign key intent where helpful (Snowflake does not enforce constraints by default).
- Quote identifiers only when necessary.
- Include lineage columns back to source Data Vault hash keys and load metadata.
- Include effective dating columns for SCD Type 2 dimensions.
- Avoid embedding business-rule transformations that belong in dbt or ETL SQL.

## Dimensional DDL Output

Generate files under:

```text
dv2_to_dimensional/outputs/dimensional_ddl/
  dim_*.sql
  fact_*.sql
  bridge_*.sql
```

For each table include:

- table-level comment explaining business purpose and grain
- surrogate key column where appropriate
- natural/business key columns where appropriate
- role-playing date keys where relevant
- Data Vault lineage columns such as source hash key, load timestamp, and record source
- current/effective dating columns for SCD Type 2 dimensions

## Manifest Expectations

The manifest should capture:

- source vault objects and classifications
- target dimensional objects
- object-level mapping from vault to dimensional layer
- fact grain statements
- SCD decisions
- assumptions
- open questions
- excluded or unresolved objects

## Microsite Expectations

The microsite should be a concise implementation briefing:

- executive summary
- source vault inventory
- target dimensional model
- grain decisions
- SCD strategy
- mapping table
- generated DDL list
- governance and lineage notes
- validation checks
- unresolved questions and blockers

## Validation

- Every target table has a documented source mapping.
- Every fact has one declared grain.
- Every dimension has an SCD decision.
- No source object silently disappears; each is mapped, excluded, or flagged.
- DDL is Snowflake-compatible.
- Manifest and microsite agree with the generated DDL.
