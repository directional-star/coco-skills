# Discovery

Begin each non-trivial conversion with a short interactive discovery phase.

## Snowflake-First Introspection

Before asking questions, inspect the source schema directly:

1. Run `SHOW TABLES IN SCHEMA <db>.<schema>` via `sql_execute` to inventory all objects.
2. Classify objects by naming convention: `HUB_*`, `LNK_*`, `SAT_*`, `PIT_*`, `BRG_*`, `REF_*`.
3. Use `SELECT GET_DDL('TABLE', '<fqn>')` to pull DDL for key entities.
4. Use `DESCRIBE TABLE <fqn>` to inspect column types and identify hash keys, business keys, load timestamps, and descriptive attributes.
5. Use `cortex lineage <fqn> --direction downstream --distance 2` to identify existing marts or consumers.

If local files are provided instead, inspect them before asking questions.

## Rules

- Ask one question at a time.
- Provide a recommended answer with each question.
- Inspect files/schema before asking anything discoverable from DDL, metadata, SQL, or dbt configuration.
- Persist answers into `outputs/manifests/conversion_manifest.yml` under `user_decisions` and `assumptions`.
- Ask only material questions. For low-impact uncertainty, proceed and document the assumption.

## Baseline Questions

1. What business processes should the dimensional model prioritize?
2. Which entities are core business keys versus descriptive/reference entities?
3. What fact grains are expected or already known?
4. What SCD behavior is required for each major dimension?
5. Should PIT, bridge, reference, and business vault objects influence the design?
6. Are there downstream `.dv.sql` queries that must remain result-equivalent?
7. Should dbt output follow existing naming and materialization conventions?
8. Are there entities or attributes that must be excluded, masked, or renamed?
9. What output folder should be used?
10. Should conversion proceed with best-effort assumptions for unresolved items?

## Question Format

```text
Question N: <specific decision>

Recommended answer: <recommended choice based on inspected schema/files and modeling constraints>.

Reason: <short concrete rationale>.
```

Stop the discovery phase once enough information exists to produce a useful draft.

