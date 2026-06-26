---
name: dv2-to-dimensional
description: "Convert Snowflake Data Vault 2.0 models into Kimball-style dimensional designs. Use when: converting DV2 to dimensional DDL, rewriting Data Vault SQL to dimensional SQL, converting dbt Data Vault to dimensional dbt, or generating a flight-booking demo. Triggers: data vault, dv2, dimensional, kimball, hub to dimension, link to fact, vault conversion."
---

# DV2 to Dimensional

Convert Snowflake Data Vault 2.0 models, downstream `.dv.sql` queries, and dbt Data Vault projects into Kimball-style dimensional designs with DDL, dbt models, query rewrites, manifests, and documentation.

## Tools

This skill leverages Cortex Code's native Snowflake connectivity:

| Tool | Usage |
|------|-------|
| `sql_execute` | Run `GET_DDL('TABLE', ...)`, `SHOW TABLES IN SCHEMA`, `DESCRIBE TABLE`, deploy generated DDL, validate queries with `EXPLAIN` |
| `cortex search object` | Discover Data Vault objects in a schema by naming pattern (HUB_, LNK_, SAT_, PIT_, BRG_) |
| `cortex lineage` | Trace upstream/downstream dependencies of DV objects to understand data flow |
| `fdbt` | Parse dbt project structure, list models, check lineage and test coverage (Workflow 3) |
| `read` / `glob` | Read local DDL, SQL, metadata, dbt files from workspace |

## Operating Rules

- Be interactive: start with a short discovery phase, ask one question at a time, include a recommended answer.
- **Inspect Snowflake live first**: use `sql_execute` to introspect schemas and pull DDL before asking questions whose answers are discoverable.
- Also inspect local DDL, metadata, SQL, dbt files, and existing outputs before asking questions.
- Read from `dv2_to_dimensional/inputs/` and write to `dv2_to_dimensional/outputs/` by default. Do not overwrite source files unless the user explicitly asks.
- Generate best-effort drafts when business context is incomplete, but mark assumptions and open questions clearly.
- Keep Workflow 1 to dimensional DDL and documentation only. Transformation SQL belongs in Workflow 3's dbt models.

## Workflow Decision

- **Data Vault model to dimensional DDL/docs**: read [references/workflow-dimensional-ddl.md](references/workflow-dimensional-ddl.md)
- **Downstream `.dv.sql` query conversion**: read [references/workflow-query-conversion.md](references/workflow-query-conversion.md)
- **dbt Data Vault to dimensional dbt layer**: read [references/workflow-dbt-conversion.md](references/workflow-dbt-conversion.md)
- **Flight-booking demo**: read [references/workflow-demo.md](references/workflow-demo.md)

Always read [references/discovery.md](references/discovery.md) before beginning a conversion, and [references/conversion-rules.md](references/conversion-rules.md) before making modeling decisions.

## Required Workspace Shape

Prefer this layout unless the user gives a different path:

```text
dv2_to_dimensional/
  inputs/
    ddl/
    metadata/
    queries/
    dbt/
  outputs/
    dimensional_ddl/
    query_conversions/
    dbt/
    docs/
    manifests/
    demo/
```

Create missing output directories as needed. Preserve original inputs.

## Required Artifacts

- Snowflake dimensional DDL under `outputs/dimensional_ddl/`
- Single-page HTML microsite under `outputs/docs/index.html`
- Machine-readable manifest under `outputs/manifests/conversion_manifest.yml`
- Converted dimensional queries under `outputs/query_conversions/*.dm.sql` (Workflow 2)
- Plain dbt dimensional layer under `outputs/dbt/models/marts/dimensional/` (Workflow 3)

Use [assets/conversion_manifest.template.yml](assets/conversion_manifest.template.yml) as the manifest structure and [assets/microsite.template.html](assets/microsite.template.html) as the documentation structure.

## Snowflake Introspection Patterns

When the user provides a database/schema instead of local files, use these patterns:

```sql
-- Discover all DV objects in a schema
SHOW TABLES IN SCHEMA <db>.<schema>;

-- Pull DDL for a specific table
SELECT GET_DDL('TABLE', '<db>.<schema>.<table>');

-- Inspect columns
DESCRIBE TABLE <db>.<schema>.<table>;

-- Check relationships via naming conventions
-- HUB_*, LNK_*, SAT_*, PIT_*, BRG_*, REF_*
```

Use `cortex search object "<pattern>"` to find objects across schemas, and `cortex lineage <db.schema.table> --direction both` to trace data flow.

## Completion Checks

Before finishing, verify:

- Every Data Vault entity is mapped, excluded, or flagged
- Every fact table has a declared grain
- Every dimension has SCD guidance
- Snowflake DDL is syntactically valid (use `sql_execute` with `only_compile=true` or wrap in `EXPLAIN` where possible)
- The manifest and microsite agree with the generated DDL
- Query conversions preserve exact result equivalence where provable, or list blockers
- dbt output uses plain dbt and does not destructively edit the source project

## Stopping Points

- After discovery phase: confirm business process priorities and key decisions
- After dimensional model proposal: confirm dimensions, facts, bridges before generating DDL
- After DDL generation: review before optional deployment to Snowflake
