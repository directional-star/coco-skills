# Workflow 2: Downstream Data Vault SQL To Dimensional SQL

Use this workflow to convert downstream `.dv.sql` queries that currently read Data Vault 2.0 structures into equivalent queries against the generated dimensional layer.

## Inputs

Read from:

- `dv2_to_dimensional/inputs/queries/*.dv.sql`
- `dv2_to_dimensional/inputs/ddl/*.sql`
- `dv2_to_dimensional/outputs/manifests/conversion_manifest.yml`
- optional target dimensional DDL under `outputs/dimensional_ddl/`

## Outputs

Write converted queries to:

```text
dv2_to_dimensional/outputs/query_conversions/
  <query_name>.dm.sql
```

For each conversion also produce a short note explaining:

- source query purpose
- target dimensional objects used
- preserved filters, joins, and aggregations
- known equivalence risks
- validation SQL where possible

## Rules

- Inspect source SQL before asking questions.
- Preserve the analytical intent of the source query.
- Prefer simple dimensional joins over reproducing Data Vault join complexity.
- Keep filters, date logic, and aggregation semantics equivalent.
- Do not claim exact equivalence unless validated.
- If a source query relies on raw vault load-time semantics, PIT logic, effectivity windows, or non-obvious hash-key joins, flag the risk explicitly.
- Use manifest mappings to translate hubs, links, satellites, PITs, and bridges to dimensions, facts, and dimensional bridges.

## Steps

1. **Classify source query**: reporting query, reconciliation query, operational extract, data quality query, or ad hoc exploration.
2. **Identify Data Vault dependencies**: list hubs, links, satellites, PITs, bridges, and reference tables used.
3. **Map dependencies**: use the conversion manifest to identify dimensional target tables.
4. **Rewrite SQL**: produce dimensional SQL with clear joins and aliases.
5. **Validate semantics**: compare grain, row counts, measure totals, filters, null handling, and date windows where possible.
6. **Document blockers**: list any unresolved equivalence risks.

## Validation Checks

Where source access is available, generate comparison SQL for:

- row counts at the intended grain
- measure totals
- distinct business key counts
- date-filtered slices
- null distribution for key attributes
- top-N comparison for major dimensions

## Completion Criteria

- Each `.dv.sql` query has a corresponding `.dm.sql` output.
- The converted query references dimensional objects only.
- The conversion note explains assumptions and blockers.
- Equivalence is either validated or explicitly marked as unproven.
