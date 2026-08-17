<!-- markdownlint-disable -->

# Hardening Report: crazy-max--ghaction-dockerhub-mirror/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **crazy-max--ghaction-dockerhub-mirror/v1.0.0** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The action.yml run: block directly interpolates multiple ${{ inputs.* }} expressions inside shell commands (sub-rule a). An attacker-controlled input value containing shell metacharacters (e.g. `;`, `$(...)`, backticks) would be executed by bash. Affected expressions: `${{ inputs.dockerhub-username }}`, `${{ inputs.dockerhub-password }}`, `${{ inputs.dockerhub-repo }}`, `${{ inputs.dest-registry }}`, `${{ inputs.dest-repo }}`, and `${{ inputs.dry-run }}`. These should be moved to an `env:` block and referenced as quoted shell variables (e.g. `"$DOCKERHUB_USERNAME"`) instead.

Locations:

- `action.yml:33`

### unpinned-uses (severity: high)

All uses: references in the workflow files use mutable version tags instead of pinned 40-character commit SHAs, making the workflows vulnerable to supply-chain attacks if the referenced action tags are moved. Failing references in ci.yml: `actions/checkout@v2.3.3`, `docker/setup-qemu-action@v1`, `docker/setup-buildx-action@v1`, `docker/login-action@v1`. Failing references in labels.yml: `actions/checkout@v2.3.3`, `crazy-max/ghaction-github-labeler@v3.1.0`.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:27`
- `.github/workflows/labels.yml:14`
- `.github/workflows/labels.yml:17`

### missing-permissions (severity: medium)

Neither ci.yml nor labels.yml defines a top-level `permissions:` key, and no individual jobs within these workflows define job-level `permissions:` keys. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/labels.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dockerhub-username }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:37`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dockerhub-password }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:38`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dockerhub-repo }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:39`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dest-registry }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:40`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dest-repo }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:41`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dry-run }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across 3 files:

1. action.yml (script-injection / static-inline-injection): Moved all 6 `${{ inputs.* }}` expressions out of the run: shell block into an `env:` block (INPUT_DOCKERHUB_USERNAME, INPUT_DOCKERHUB_PASSWORD, INPUT_DOCKERHUB_REPO, INPUT_DEST_REGISTRY, INPUT_DEST_REPO, INPUT_DRY_RUN). The shell script now references these as quoted environment variables (e.g. "$INPUT_DOCKERHUB_USERNAME") to prevent shell metacharacter injection.

2. .github/workflows/ci.yml (unpinned-uses + missing-permissions): Pinned all 4 action references to full commit SHAs (actions/checkout@a81bbbf8, docker/setup-qemu-action@27d0a4f1, docker/setup-buildx-action@f211e3e9, docker/login-action@dd4fa067). Added top-level `permissions: contents: read` and job-level permissions block.

3. .github/workflows/labels.yml (unpinned-uses + missing-permissions): Pinned both action references to full commit SHAs (actions/checkout@a81bbbf8, crazy-max/ghaction-github-labeler@dbccbd0e). Added top-level and job-level `permissions: contents: read, issues: write` (issues:write needed for the labeler action to manage labels).

