## Why

WTA-4105: additive fields in persisted HITLv2 JSON currently break older readers. Shared-database deployments need tolerant readers for existing variants and explicit version constraints.

## What Changes

- Ignore additive fields in read-only snapshots and known callback/locator versions.
- Allow additive fields when loading credential plaintext and delivery targets.
- Preserve target extensions through the existing locator update path.
- Keep the credential envelope strict and retain existing HTTP DTO and callback version validation.
- Keep credential update implementations unchanged; this change does not promise retention of unknown credential fields across an update.

## Capabilities

### New Capabilities

- `hitl-v2-json-compatibility`: additive JSON reads, target preservation and release constraints across HITLv2 persistence codecs.

### Modified Capabilities

- `human-input-email-channel-management`: permit extension fields when reading encrypted Resend plaintext; sender settings remain separate.

## Impact

HITLv2 persistence model configuration and provider codecs. No database migration, credential update change, new provider, HTTP input relaxation or global JSON-column change.
