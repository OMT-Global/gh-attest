# gh-attest

Reusable GitHub Actions workflow that generates a signed AI attestation for the
current commit using cosign keyless signing and uploads it as a build artifact.

## What it does
- Writes an attestation JSON file for the current commit SHA
- Signs the attestation with cosign keyless signing (OIDC)
- Uploads the JSON + signature + certificate as an artifact
- Verifies the signature in a follow-up job

## Usage
```yaml
name: ai-attestation

on:
  pull_request:
  push:
    branches: [ main ]

jobs:
  attest:
    uses: jmcte/gh-attest/.github/workflows/ai-attestation.yml@main
    with:
      artifact_name: ai-attestation
      retention_days: 90
      ai_provider: OpenAI
      ai_model: gpt-5.1-code
      prompt_hash: unknown
```

## Inputs
- artifact_name: Base name for the uploaded artifact (default: ai-attestation)
- retention_days: Artifact retention in days (default: 90)
- ai_provider: Name of the AI provider (default: unknown)
- ai_model: Model identifier (default: unknown)
- prompt_hash: Hash or identifier for the prompt (default: unknown)

## Outputs
- sha: The commit SHA used for the attestation

## Artifacts
The workflow writes these files to `.attest/` and uploads them:
- `.attest/<sha>.json`
- `.attest/<sha>.sig`
- `.attest/<sha>.crt`

## Notes
- The workflow requests `id-token: write` and `contents: read` permissions for keyless signing.
