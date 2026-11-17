## 1. Node metadata and DSL validation

- [x] 1.1 Add failing regression tests for version-correct descriptions/defaults and field-located v2 DSL errors.
- [x] 1.2 Implement version-aware schema/default selection and side-effect-free authoring validation while preserving v1 drafts.
- [x] 1.3 Share the existing standard notification template between node defaults and migration while preserving custom templates and required input fields.

## 2. OpenAPI form operations

- [x] 2.1 Add failing read/submit and authorization regression tests with persisted v2 forms.
- [x] 2.2 Implement app-scoped v2 OpenAPI read/submit, legacy fallback, response mapping, and follow-run hints.
- [x] 2.3 Bind OpenAPI EndUser initiators and verify exact actor authorization and first-success resume behavior.

## 3. Verification

- [x] 3.1 Run focused and related unit tests, formatting/lint, and scoped type checks.
- [x] 3.2 Validate OpenSpec and inspect the final diff for unrelated changes and compatibility regressions.

Verification: 375 focused and related backend unit tests pass, changed Python files pass Ruff and Pyrefly, and all 102 import contracts pass. OpenAPI contracts and Markdown were regenerated; contract TypeScript checking and strict OpenSpec validation pass. Docker-backed backend integration suites were not run locally.

CLI E2E (2026-11-17): Verified against an isolated patched API using hitl-dev's real database, Redis, and workflow workers, based on deployment d1630551880e. Real difyctl processes verified v2 node description, a valid DSL and five invalid variants with field locations, Account-run suspension, form read/submission, resumed completion with the submitted output, and duplicate-submit rejection (409 already_submitted). Chrome device authorization succeeded after temporarily enabling OpenAPI and adding the missing nginx route. The temporary API and nginx changes were removed and the test login revoked. External SSO was not exercised end-to-end.

Default-template follow-up: 106 node-description, migration, runtime-node, and DSL-check tests pass. The default description uses the existing standard template; custom migrated templates and missing subject/body errors remain covered. Ruff, scoped Pyrefly, and strict OpenSpec validation pass. This follow-up did not repeat the live CLI E2E.
