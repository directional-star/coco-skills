# Conversion Rules

## Data Vault Entity Classification

Distinguish raw vault, business vault, and information mart objects before mapping:

- Raw hubs, links, and satellites define core business keys, relationships, and descriptive history.
- Business vault PITs, bridges, effectivity satellites, derived satellites, and calculated structures inform access paths and business rules.
- Existing marts are hints only. Do not blindly reproduce them.

## Decision Matrix

- Hub plus descriptive satellites usually becomes a dimension candidate.
- Transaction or event links may become fact candidates when they represent a measurable business process.
- Relationship-only many-to-many links may become bridge tables or factless facts.
- Link satellites may contribute measures, statuses, degenerate dimensions, or fact attributes at the link grain.
- Effectivity satellites influence SCD behavior and valid-date relationship handling.
- PIT tables guide latest-record joins but do not automatically become dimensional tables.
- Business vault bridges can inform many-to-many dimensional bridges.
- Reference tables may become dimensions, outriggers, or decoded attributes depending on reuse, cardinality, and reporting needs.
- Date/time attributes should map to role-playing date/time dimensions where appropriate.

## Dimensional Patterns

Default to Kimball-style conformed dimensions and fact tables, but use established dimensional patterns when justified:

- role-playing dimensions
- junk dimensions
- degenerate dimensions
- bridge tables
- factless facts
- accumulating snapshots
- periodic snapshots
- outriggers, only when they reduce duplication without hurting usability

## SCD Guidance

- Preserve historized semantics from satellites and effectivity satellites.
- Recommend SCD Type 2 for descriptive attributes where business reporting needs history.
- Recommend SCD Type 1 for corrections or attributes that should not be historized.
- Mark the choice as an assumption when metadata does not specify behavior.

## Guardrails

Do not:

- Invent measures not present in source structures or metadata.
- Treat every link as a fact without grain validation.
- Flatten all satellites into dimensions without SCD reasoning.
- Lose temporal semantics from satellites, PITs, or effectivity satellites.
- Claim exact SQL equivalence without validation or blocker reporting.
- Generate dbt transformations by ignoring missing relationships.

