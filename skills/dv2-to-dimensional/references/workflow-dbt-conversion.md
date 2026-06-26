# Workflow 3: dbt Data Vault To Dimensional dbt Layer

Use this workflow to generate a plain dbt dimensional layer from Data Vault dbt models.

## Inputs

Read from:

- `dv2_to_dimensional/inputs/dbt/`
- Optional `dv2_to_dimensional/outputs/manifests/conversion_manifest.yml`
- Optional metadata and user decisions

## Tools

Use these Cortex Code tools for dbt introspection:

| Command | Purpose |
|---------|---------|
| `fdbt info` | Get dbt project metadata (name, version, paths) |
| `fdbt list` | List all models, sources, tests, seeds |
| `fdbt lineage <model> -u` | Show upstream dependencies of a model |
| `fdbt tests coverage` | Check test coverage across models |

## Outputs

Write to:

```text
dv2_to_dimensional/outputs/dbt/models/marts/dimensional/
  dim_*.sql
  fact_*.sql
  bridge_*.sql
  schema.yml
```

Do not destructively edit the source dbt project.

## Rules

- Use plain dbt by default: `ref()`, `source()`, materializations, model SQL files, and built-in generic tests.
- Do not require packages such as `dbt_utils` unless the project already uses them or the user explicitly approves.
- Follow existing naming and materialization conventions when they are discoverable.
- Put transformation SQL here, not in Workflow 1.
- Preserve dimensional grain and SCD decisions from the manifest.

## Steps

1. **Inspect project**: Use `fdbt info` and `fdbt list` to understand project structure. Also inspect `dbt_project.yml`, model folders, existing `schema.yml` files, macros, snapshots, and package dependencies.
2. **Trace lineage**: Use `fdbt lineage` to identify Data Vault model layers and their relationships.
3. **Map targets**: Use the manifest or run Workflow 1 decisions to determine target dimensions, facts, and bridges.
4. **Generate model SQL**: Create models using `ref()` and `source()` as appropriate.
5. **Generate schema.yml**: Include model descriptions, column descriptions, and generic tests.
6. **Add relationship tests**: Where target tables and keys are clear.
7. **Compile check**: Run `dbt compile` if a working dbt environment is available; otherwise document that compile was not run. Use `fdbt tests coverage` to verify test coverage.

## schema.yml Expectations

Include:

- model descriptions
- column descriptions
- uniqueness and not-null tests for surrogate keys and natural keys where appropriate
- relationship tests for foreign keys where reliable
- accepted values tests only when values are known from metadata or reference data

## Validation

- Generated models match manifest targets.
- `schema.yml` exists and includes tests/docs.
- Existing dbt source files remain unchanged.
- Models compile where possible.
- `fdbt tests coverage` shows adequate coverage for new models.
- Missing dependencies, profiles, packages, or database access are documented.

