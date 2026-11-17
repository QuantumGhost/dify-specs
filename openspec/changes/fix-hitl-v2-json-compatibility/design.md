## Context

WTA-4105 defines additive JSON compatibility for known HITLv2 variants. The requested baseline is a4eb33275f5d; it lacks the earlier PR's ResolvedForm configuration and envelope note, so this change includes those prerequisites. The requester limited this delivery to model configuration and reverted the proposed credential model_copy updates.

## Goals / Non-Goals

Allow existing variants with additive fields to be read while preserving known-field validation. Keep approval semantics, provider authentication, credential updates and envelope validation intact. Unknown credential field retention across updates is outside this delivery.

## Decisions

- Read-only ResolvedForm parts/actions, sync/reconciliation snapshots, audit payloads and recipient sources ignore extensions. State updates never assign their parsed JSON back to those columns.
- Resend and six provider plaintext credential types allow extensions during decoding. HTTP DTOs retain their existing validation and responses explicitly project known fields. Credential update implementations retain their existing reconstruction behavior.
- All three target snapshot variants allow extensions. The existing set_im_message_locator implementation already uses model_copy and retains target extensions; no repository implementation change is needed.
- Five private locator readers and internal button metadata ignore extensions. Persisted locator strings and inbox payloads are retained verbatim. Known required fields and discriminators remain validated.
- Keep existing callback version validation and consumer behavior. Unsupported metadata versions remain invalid; the existing consumer treats decoding errors as consumed. Version bumps require compatible readers before writers and cannot rely on retries.
- The encrypted envelope remains exact version 1 with forbid. A format change requires explicit decoders; additive plaintext fields do not change the envelope.

## Risks / Trade-offs

- A future write path that overwrites parsed read-only snapshots would lose ignored fields. Business tests guard current state updates.
- Credential reconstruction can discard unknown plaintext fields during updates. Tolerant credential reads alone do not establish compatibility for an older process updating credentials that contain newer fields.
- Incompatible callback versions can be consumed as invalid. Deployment must prevent newer callbacks from reaching incompatible workers.
- Local tests do not prove production multi-version deployment, database locking or provider behavior.

## Migration Plan

1. Deploy tolerant readers before enabling additive writers and confirm incompatible API/worker processes have exited.
2. Rollback readers must support the fields and versions already written. Credential format changes must account separately for the existing update behavior; this change provides no preservation guarantee across credential updates.
3. Deploy new envelope and callback decoders before enabling new-version writers. Never roll back to unsupported envelope readers. For callback rollback, retain compatible workers until pending callbacks are drained and live cards of the newer version can no longer produce callbacks.
4. New discriminators and approval/authentication semantic changes require their own compatibility design.

## Validation

Validation covers additive root/nested/null fields, historical reads, strict envelope rejection, credential decoding, target roundtrips, locator decoding, unchanged form/sync/audit JSON and authenticated callback payload retention. Repeated field-shape dimensions use one representative payload per model/decoder. Backend integration tests and real rolling deployment are not run locally.

Affected unit suites passed: 582 tests. The unchanged credential/channel service regression suites also passed (142 tests). Ruff check/format check, production and strict unit-test Pyrefly checks, git diff checks and OpenSpec strict validation passed.
