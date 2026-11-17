## 1. Persistence compatibility

- [x] 1.1 Apply ignore to read-only snapshots and allow to credential/target models; keep envelope strict.
- [x] 1.2 Preserve the existing credential update implementations and target locator update path.

## 2. Provider codecs

- [x] 2.1 Read additive known-version locator/button metadata without rewriting opaque payloads.
- [x] 2.2 Keep current callback version validation and document deployment order for incompatible versions.

## 3. Verification and release

- [x] 3.1 Verify historical/additive reads, invalid known fields, credential decoding, target roundtrips and untouched read-only JSON.
- [x] 3.2 Verify authenticated additive callback persistence and unchanged known-version validation.
- [x] 3.3 Document reader-first deployment and the scope of credential compatibility.
- [x] 3.4 Run targeted unit tests, lint/type checks and OpenSpec validation.
