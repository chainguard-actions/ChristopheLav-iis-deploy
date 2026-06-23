<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ChristopheLav--iis-deploy/v1.0.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The run: block in action.yml directly interpolates multiple GitHub Actions expressions into a PowerShell command string, violating sub-rule (a). The following expressions are interpolated without any quoting or sanitization: `${{ github.action_path }}` (line 25), `${{ inputs.source-path }}` (line 26), `${{ inputs.msdeploy-service-url }}` (line 27), `${{ inputs.website-name }}` (line 28), `${{ inputs.msdeploy-username }}` (line 29), and `${{ inputs.msdeploy-password }}` (line 30). An attacker-controlled caller can supply values containing shell metacharacters, newlines, or PowerShell injection sequences (e.g. `; Invoke-Expression ...`) that will be executed verbatim by the runner. All inputs should be passed via environment variables and referenced as `$Env:VAR_NAME` inside the script, never interpolated directly into the run: string.

Locations:

- `action.yml:24`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.source-path }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-service-url }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.website-name }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-username }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.msdeploy-password }}" appears directly in run: block of step "Deploy Application ${{ inputs.website-name }} on IIS"; move to env: map

Locations:

- `action.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all script injection vulnerabilities in action.yml by moving all ${{ }} expressions out of the run: block and into an env: block. The six expressions (${{ github.action_path }}, ${{ inputs.source-path }}, ${{ inputs.msdeploy-service-url }}, ${{ inputs.website-name }}, ${{ inputs.msdeploy-username }}, ${{ inputs.msdeploy-password }}) are now assigned to environment variables (ACTION_PATH, SOURCE_PATH, MSDEPLOY_SERVICE_URL, WEBSITE_NAME, MSDEPLOY_USERNAME, MSDEPLOY_PASSWORD) and referenced as $Env:VAR_NAME in the PowerShell script. The step name was also cleaned up to remove the interpolated ${{ inputs.website-name }} expression.

