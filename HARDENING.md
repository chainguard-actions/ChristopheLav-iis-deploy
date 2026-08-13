<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ChristopheLav--iis-deploy/v1.1.1** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The run: block in action.yml directly interpolates multiple ${{ inputs.* }} expressions into the PowerShell shell command string (sub-rule a). The values of inputs.source-path, inputs.msdeploy-service-url, inputs.website-name, inputs.msdeploy-username, inputs.msdeploy-password, and inputs.skip-extra-files are all substituted verbatim into the command before PowerShell executes it. An attacker-controlled input containing shell metacharacters (semicolons, parentheses, backticks, etc.) can break out of the intended command and execute arbitrary code. Additionally, ${{ github.action_path }} is interpolated directly, which — while less attacker-controlled — still violates the rule that no ${{ ... }} expression should appear inside a run: block. Fix: move all inputs into env: variables and reference them as quoted PowerShell variables (e.g., $env:SOURCE_PATH) inside the script, or pass them as named parameters with proper quoting.

Locations:

- `action.yml:23`

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

Fixed all script injection findings in action.yml by moving all ${{ inputs.* }} and ${{ github.action_path }} expressions out of the run: block and into an env: block. The PowerShell run: block now references values via $env:VAR_NAME with named parameters (-packOutput, -deployUrl, -websiteName, -deployUserName, -deployUserPassword, -skipExtraFilesOnServer), preventing any attacker-controlled input from being interpreted as shell commands. The step name was also simplified to remove the inline ${{ inputs.website-name }} interpolation.

