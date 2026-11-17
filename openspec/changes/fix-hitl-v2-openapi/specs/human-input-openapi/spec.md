## ADDED Requirements

### Requirement: Human Input descriptions MUST match the advertised version

`describe.node_type` MUST return the latest Human Input version's schema and editor defaults. Version 2 MUST describe `recipients_spec`, `message_template`, and `debug_mode`, and MUST NOT describe legacy `delivery_methods`. Filling a v2 draft MUST NOT add legacy delivery configuration. Version 1 drafts MUST retain their existing schema and defaults.

#### Scenario: Describe the latest Human Input node
- **WHEN** Dify CLI describes Human Input on a server whose latest version is 2
- **THEN** the schema and nonempty default configuration MUST use the v2 wire shape

#### Scenario: Default notification template
- **WHEN** Dify CLI reads the v2 node's default configuration
- **THEN** `message_template` MUST use the existing standard template: subject `Action required` and body `A request is waiting for your review. Please review the details and submit your decision to continue. Review and respond: {{#url#}}`
- **AND** explicit custom templates in migrated nodes MUST remain unchanged

### Requirement: DSL checks MUST locate Human Input v2 authoring errors

DSL checks and imports MUST validate v2 structural errors with the v2 schema. They MUST report missing/invalid fields at `nodes.<id>.data.<field>`. They MUST reject an empty recipient list, a blank message subject/body, a body without exactly one `{{#url#}}`, and enabled debug mode without a channel. Checking MUST NOT initialize forms, send messages, or activate debug delivery.

#### Scenario: Missing v2 fields
- **WHEN** a v2 node omits `recipients_spec`, `message_template`, or `debug_mode`
- **THEN** the checker MUST return a separate field-located error for every omitted field

#### Scenario: Invalid static configuration
- **WHEN** a structurally valid v2 draft violates one of the listed static rules
- **THEN** the checker MUST reject it at the relevant recipient, template, or debug-channel field

### Requirement: OpenAPI MUST read and submit authorized v2 initiator forms

Existing OpenAPI form operations MUST support v2 Current Initiator tokens and preserve v1 behavior. Account callers MUST use their existing `AccountId` and Console recipient authorization without introducing a tenant/app-bound Account actor. External SSO EndUsers MUST match the original app-owned EndUser grant. Controllers MUST check that the authorized form belongs to the route's app before returning its definition or accepting a submission. Unrelated actors, notification deliveries, and public Email OTP tokens MUST be rejected without returning form data or modifying form state.

Runtime composition MUST distinguish `UserFrom.ACCOUNT` from `UserFrom.END_USER` independently of `InvokeFrom.OPENAPI`; both caller kinds MUST retain their own initiator grant.

Successful reads MUST use the existing OpenAPI form response fields. Successful submissions MUST use the v2 first-success transaction and post-commit resume path and return the existing follow-run hint. Rejected or repeated submissions MUST NOT overwrite an accepted result or enqueue another resume.

#### Scenario: Account approves its paused run
- **WHEN** the authorized Account reads and submits its active v2 Current Initiator token
- **THEN** OpenAPI MUST return the form, persist the accepted decision, and return a hint to follow the resumed run

#### Scenario: External SSO initiator approves its paused run
- **WHEN** the original External SSO EndUser reads and submits its v2 Current Initiator token
- **THEN** OpenAPI MUST authorize that exact EndUser and record it as the submission actor

#### Scenario: Unrelated caller or token
- **WHEN** a different actor or a notification/public token is presented
- **THEN** OpenAPI MUST return form-not-found without changing the form or resuming the workflow

#### Scenario: Active form belongs to another app
- **WHEN** an authenticated caller presents an active form through another app's route
- **THEN** the controller MUST return form-not-found without returning the definition or accepting the decision
