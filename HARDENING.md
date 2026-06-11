<!-- markdownlint-disable -->

# Hardening Report: crazy-max--ghaction-dockerhub-mirror/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **crazy-max--ghaction-dockerhub-mirror/v1.0.0** was hardened automatically. 7 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Six `${{ inputs.* }}` expressions are interpolated directly inside the `run:` shell block. Before the shell executes, GitHub Actions substitutes these values verbatim into the script text, allowing an attacker-controlled input to inject arbitrary shell commands.

Offending lines:
- Line 35: `DOCKERHUB_USERNAME=${{ inputs.dockerhub-username }}`
- Line 36: `DOCKERHUB_PASSWORD=${{ inputs.dockerhub-password }}`
- Line 37: `DOCKERHUB_REPO=${{ inputs.dockerhub-repo }}`
- Line 38: `DOCKER_DEST_REGISTRY=${{ inputs.dest-registry }}`
- Line 39: `DOCKER_DEST_REPO=${{ inputs.dest-repo }}`
- Line 42: `if [ "${{ inputs.dry-run }}" = "false" ]; then`

Fix: Move each input into an `env:` block and reference it as a quoted shell variable (e.g. `"$DOCKERHUB_USERNAME"`) inside the `run:` script.

Locations:

- `action.yml:35`
- `action.yml:36`
- `action.yml:37`
- `action.yml:38`
- `action.yml:39`
- `action.yml:42`

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

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved all six ${{ inputs.* }} expressions from the run: shell block into an env: block on the step in action.yml. The variables DOCKERHUB_USERNAME, DOCKERHUB_PASSWORD, DOCKERHUB_REPO, DOCKER_DEST_REGISTRY, DOCKER_DEST_REPO, and DRY_RUN are now set via the env: map and referenced as plain shell variables inside the run: script. The inline assignment lines (e.g. DOCKERHUB_USERNAME=${{ inputs.dockerhub-username }}) were removed, and the dry-run check was updated from if [ "${{ inputs.dry-run }}" = "false" ] to if [ "$DRY_RUN" = "false" ].

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability on line 57 of action.yml. The original code used unsafe shell string concatenation ('${DOCKERHUB_USERNAME}' and '${DOCKERHUB_PASSWORD}') that broke out of single-quoted strings, allowing shell metacharacter injection via input values. Replaced with `jq -n --arg u "$DOCKERHUB_USERNAME" --arg p "$DOCKERHUB_PASSWORD" '{username:$u,password:$p}'` which safely JSON-encodes the credentials, preventing any injection attacks.

