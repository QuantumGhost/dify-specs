## Context

The OpenAPI node metadata and DSL checker share a type-only Human Input v1 override. Runtime registration selects v2. Form handlers use the v1 repository exclusively. OpenAPI admits both Accounts and External SSO EndUsers, but v2 runtime composition does not recognize the OpenAPI EndUser initiator.

## Goals / Non-Goals

**Goals:** Correct the three WTA-3927 failures while preserving v1 contracts and v2 recipient authorization, atomic submission, and post-commit resume.

**Non-Goals:** New endpoints, storage migration, provider delivery changes, and broader frontend validation redesign.

## Decisions

- Limit the legacy node-data override and legacy editor defaults to v1. Supply v2 defaults from the v2 node, using the existing standard notification template shared with legacy migration. Keep message template fields required on parsed input; selecting a default template must not change missing-field validation. Add authoring-only Pydantic models beside the existing node schema owner for recipient, message-template, and debug-channel checks. Runtime entities continue to accept incomplete editor drafts; DSL import/check rejects them with field locations.
- Add OpenAPI read/submit operations to the existing v2 submission service. Account callers pass the existing `AccountId` and reuse Console recipient authorization, which resolves the Contact in the delivery's workspace; do not bind Account identity to a tenant/app actor. EndUsers retain their existing app-owned actor and authorization. Only permit the Current Initiator delivery exposed by run streams. Controllers reuse their existing form/app ownership check before returning a definition or accepting a submission. Reuse the atomic submission path; never treat an app grant or possession of a token alone as approval authority.
- Keep v1 lookup and surface checks intact, then fall back to the v2 service. Serialize v2 presentation through the existing OpenAPI form DTO and retain the follow-run hint after submission.
- Recognize OpenAPI EndUsers when composing the v2 initiator. Check `user_from == END_USER` explicitly before checking the invocation surface; `InvokeFrom.OPENAPI` can also carry an Account. Verify both Account/OpenAPI and EndUser/OpenAPI grant identities.

## Risks / Trade-offs

- Account and EndUser actors must not be interchangeable; negative authorization tests cover both, tenant/app scope, and notification/public tokens.
- Earlier UI specs did not require Request URL. This change records the current editor and runtime rule (exactly one `{{#url#}}`) for CLI authoring without changing runtime policy.
- Backend integration suites remain CI-only. Local regression tests exercise existing initialized unit-test storage and the real submission service.
