<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ChristopheLav--iis-deploy/v1.1.1** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ ... }} expressions are directly interpolated into the run: shell command in action.yml. The step passes all inputs as positional arguments via direct expression interpolation: `${{ github.action_path }}/scripts/PublishAspNet5Website.ps1`, `${{ inputs.source-path }}`, `${{ inputs.msdeploy-service-url }}`, `${{ inputs.website-name }}`, `${{ inputs.msdeploy-username }}`, `${{ inputs.msdeploy-password }}`, `${{ inputs.skip-extra-files }}`. An attacker controlling any of these inputs (e.g. website-name, source-path, msdeploy-password) can inject arbitrary PowerShell commands before the shell ever parses them. These values should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `action.yml:27`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.source-path }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-service-url }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.website-name }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-username }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-password }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip-extra-files }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:29`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all script injection findings in action.yml by moving all ${{ ... }} expressions (github.action_path, inputs.source-path, inputs.msdeploy-service-url, inputs.website-name, inputs.msdeploy-username, inputs.msdeploy-password, inputs.skip-extra-files) from the run: block into an env: block. The run: block now references these values as PowerShell environment variables ($env:ACTION_PATH, $env:SOURCE_PATH, etc.), preventing any attacker-controlled input from being interpolated directly into the shell command. The step name was also updated to remove the ${{ inputs.website-name }} expression.

