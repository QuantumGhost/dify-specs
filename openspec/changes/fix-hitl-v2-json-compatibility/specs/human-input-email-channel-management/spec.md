## MODIFIED Requirements

### Requirement: Email MUST reuse the shared Channel credential envelope

Email MUST reuse the ChannelEncryptedCredentials envelope and persistence contract defined by human-input-v2-im-control-plane-core. Its decrypted Resend credentials MUST contain api_key and MUST accept unknown extension fields during decoding. sender_email and sender_name MUST remain top-level configuration fields stored in their existing columns; extension fields MUST NOT be interpreted as configuration settings.
Credential serialization and deserialization MUST preserve the API key string exactly without interpreting or validating its provider-specific format.

#### Scenario: Complete credentials cannot be decoded

- **WHEN** decryption or Pydantic parsing of the complete credentials fails
- **THEN** the operation MUST return a safe failure without exposing credential material

#### Scenario: Stored plaintext contains newer fields

- **WHEN** credential plaintext contains unknown null or nested fields
- **THEN** decoding MUST return the known API key without rejecting the payload
