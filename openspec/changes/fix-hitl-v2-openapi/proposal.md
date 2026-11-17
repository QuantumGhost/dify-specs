## Why

WTA-3927 exposes incomplete HITLv2 support in the OpenAPI used by Dify CLI: node descriptions publish a v1 schema under version 2, DSL checks miss v2 errors, and form endpoints only read v1 storage.

## What Changes

- Select Human Input node schemas and editor defaults by version.
- Report v2 structural and static configuration errors at their field locations.
- Read and submit v2 forms through OpenAPI using the admitted Account or original EndUser, preserving v1 behavior and first-success submission semantics.

## Capabilities

### New Capabilities

- `human-input-openapi`: Version-correct node descriptions, DSL checks, and authenticated form operations for Dify CLI.

### Modified Capabilities

None.

## Impact

Backend node metadata, DSL graph validation, and OpenAPI Human Input handlers and services. No new endpoint, database migration, CLI dependency, or change to public Email OTP approval.
