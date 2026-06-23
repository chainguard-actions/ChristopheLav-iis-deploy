<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ChristopheLav--iis-deploy/v1.1.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The run: block in action.yml directly interpolates multiple ${{ inputs.* }} and ${{ github.action_path }} expressions into the PowerShell command string without any quoting or sanitization. All inputs.* values are attacker-controlled and are passed as raw positional arguments to the script, enabling command injection. Offending lines include: `${{ github.action_path }}/scripts/PublishAspNet5Website.ps1`, `${{ inputs.source-path }}`, `${{ inputs.msdeploy-service-url }}`, `${{ inputs.website-name }}`, `${{ inputs.msdeploy-username }}`, `${{ inputs.msdeploy-password }}`, `${{ inputs.skip-extra-files }}`.

Locations:

- `action.yml:28`

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

Fixed all script injection findings in action.yml by moving all ${{ inputs.* }} and ${{ github.action_path }} expressions out of the run: block into an env: block. The run: block now references environment variables using PowerShell's $env:VAR_NAME syntax. Specifically: ACTION_PATH, INPUT_SOURCE_PATH, INPUT_MSDEPLOY_SERVICE_URL, INPUT_WEBSITE_NAME, INPUT_MSDEPLOY_USERNAME, INPUT_MSDEPLOY_PASSWORD, and INPUT_SKIP_EXTRA_FILES are all set in the env: map. The skip-extra-files value is converted to a boolean using [System.Convert]::ToBoolean() since the PowerShell script expects a [bool] parameter. The step name was also simplified to remove the direct interpolation of inputs.website-name.

