## ADDED Requirements

### Requirement: Read-only HITLv2 JSON MUST tolerate additive fields
ResolvedForm and its nested parts/actions, sync contact/identity snapshots, reconciliation before/after snapshots, five audit variants and known recipient-source variants MUST ignore unknown fields, including null and nested values. Known required fields, types and discriminators MUST remain validated. State updates and appends MUST NOT overwrite the original read-only JSON with parsed values.

#### Scenario: An older reader loads an additive snapshot
- **WHEN** a writer adds optional root or nested fields to an existing variant
- **THEN** a compatible reader MUST read the known fields and subsequent state updates MUST leave the original JSON unchanged

#### Scenario: A known field or discriminator is invalid
- **WHEN** persisted JSON has an invalid known field or unsupported discriminator
- **THEN** its reader MUST reject the JSON

### Requirement: Credential readers MUST accept additive plaintext fields
Resend and the six IM provider credential plaintext models MUST allow unknown fields during decoding. HTTP request DTOs MUST retain their existing validation; responses MUST explicitly project known response fields. Retention of unknown fields across credential updates is outside this specification.

#### Scenario: Stored credential plaintext contains newer fields
- **WHEN** a supported credential payload includes unknown null and nested fields
- **THEN** decryption and model parsing MUST succeed while known credential fields remain validated

### Requirement: Delivery target updates MUST preserve unknown fields
All delivery target snapshots MUST allow unknown fields. The existing set_im_message_locator operation MUST change only its authorized known field and update timestamp while retaining every other target field and nested extension.

#### Scenario: An accepted message locator is stored
- **WHEN** set_im_message_locator updates a target containing newer extension fields
- **THEN** only the locator and existing update timestamp MUST change

### Requirement: Opaque locators and callbacks MUST retain their original values
The five provider locator readers and Slack, Feishu/Lark and Teams internal button metadata readers, including Feishu/Lark ButtonValue, MUST ignore additive fields within supported versions. Consumers MUST retain the exact persisted locator and callback strings. Identity, token, action and supported-version validation MUST remain effective.

#### Scenario: Supported callback metadata gains a field
- **WHEN** a callback contains an extra null or nested metadata field
- **THEN** the decoder MUST return the same known action, token, actor and inputs without modifying the persisted payload

#### Scenario: A callback declares an unsupported version
- **WHEN** internal metadata declares an unsupported version
- **THEN** the decoder MUST retain its existing rejection behavior
- **AND** deployment MUST prevent callbacks of that version from reaching incompatible workers by deploying compatible readers before enabling the writer and retaining them until newer pending callbacks and live cards are exhausted

### Requirement: Envelope evolution and deployment MUST respect reader support
ChannelEncryptedCredentials MUST retain forbid and exact supported-version validation. Envelope format changes MUST bump the version and have explicit decoders for each supported version. Compatible readers MUST deploy before additive writers; unsupported rollback readers MUST NOT process newer data. New discriminators and changes to approval/authentication semantics require explicit compatibility design. Credential rollout MUST account separately for the unchanged credential update behavior.

#### Scenario: An unsupported envelope is read
- **WHEN** the envelope includes an unknown field or unsupported version
- **THEN** it MUST be rejected rather than interpreted as version 1

#### Scenario: An additive writer is enabled after reader rollout
- **WHEN** all incompatible readers have exited
- **THEN** existing variants with additive fields MUST remain readable across supported upgrade and rollback versions
- **AND** credential read compatibility MUST NOT be treated as evidence of unknown field retention across updates
