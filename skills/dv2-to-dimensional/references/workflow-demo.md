# Workflow 4: Flight-Booking Demo

Use this workflow when the user asks for a demo.

## Goal

Create a compact but rich flight-booking Data Vault 2.0 example, then apply the conversion workflow to generate dimensional DDL, a manifest, and the HTML microsite.

## Demo Input Model

Create Snowflake Data Vault DDL under:

```text
dv2_to_dimensional/outputs/demo/flight_booking_dv2/inputs/ddl/
```

Include:

- Hubs: passenger, booking, flight, airport, aircraft, fare_product
- Links: booking-passenger, booking-flight, flight-route, aircraft-flight
- Satellites: passenger profile, booking status, flight schedule, airport details, fare attributes
- Reference data: booking status, cabin class, airport country/region
- Optional PIT/bridge structures where useful

## Required Hard Cases

The demo should show:

- hub-to-dimension mapping
- transaction/event link to fact mapping
- many-to-many passenger-to-booking or passenger-to-flight relationship
- role-playing airport dimensions for origin and destination
- SCD behavior from historized satellites
- reference data decoding
- lifecycle status suitable for an accumulating snapshot fact

## Demo Dimensional Output

Generate under:

```text
dv2_to_dimensional/outputs/demo/flight_booking_dv2/outputs/
  dimensional_ddl/
  docs/
  manifests/
```

Include:

- conformed dimensions
- transaction fact
- accumulating snapshot fact
- bridge or factless fact where justified
- SCD guidance
- conversion manifest
- single-page microsite

## Optional Snowflake Deployment

If the user provides a target schema, deploy the demo directly to Snowflake:

```sql
-- Deploy to a sandbox schema
CREATE SCHEMA IF NOT EXISTS <db>.<demo_schema>;
USE SCHEMA <db>.<demo_schema>;
-- Execute each DDL file via sql_execute
```

This allows the user to immediately query and explore the dimensional model.

## Validation

- Demo includes Data Vault input DDL.
- Demo includes dimensional DDL.
- Demo includes manifest and microsite.
- The demo exercises at least one hard case from the list above.
- If deployed, verify objects exist via `SHOW TABLES IN SCHEMA`.

