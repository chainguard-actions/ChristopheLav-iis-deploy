<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ChristopheLav--iis-deploy/v1.1.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates multiple GitHub Actions expressions into the PowerShell command string without any env-var indirection or quoting. All six inputs — `${{ inputs.source-path }}`, `${{ inputs.msdeploy-service-url }}`, `${{ inputs.website-name }}`, `${{ inputs.msdeploy-username }}`, `${{ inputs.msdeploy-password }}`, and `${{ inputs.skip-extra-files }}` — are attacker-controlled values that are substituted directly into the shell command before the shell ever parses it. A malicious caller can inject arbitrary PowerShell commands by supplying crafted input values (e.g. containing semicolons, backticks, or other metacharacters). `${{ github.action_path }}` is also interpolated directly, which is a context value that flows through YAML template substitution before the shell sees it. All of these must be moved to `env:` variables and then referenced as `"$ENV_VAR"` inside the script.

Locations:

- `action.yml:27`
- `action.yml:28`
- `action.yml:29`
- `action.yml:30`
- `action.yml:31`
- `action.yml:32`
- `action.yml:33`

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

Fixed all script injection findings in action.yml by moving all ${{ }} expressions out of the run: block and into an env: block. The seven expressions (${{ github.action_path }}, ${{ inputs.source-path }}, ${{ inputs.msdeploy-service-url }}, ${{ inputs.website-name }}, ${{ inputs.msdeploy-username }}, ${{ inputs.msdeploy-password }}, ${{ inputs.skip-extra-files }}) are now mapped to environment variables (ACTION_PATH, SOURCE_PATH, MSDEPLOY_SERVICE_URL, WEBSITE_NAME, MSDEPLOY_USERNAME, MSDEPLOY_PASSWORD, SKIP_EXTRA_FILES). The run: block now uses $env:VAR_NAME references in PowerShell with named parameters (-packOutput, -deployUrl, -websiteName, -deployUserName, -deployUserPassword, -skipExtraFilesOnServer) to call the existing PublishAspNet5Website.ps1 script. The step name was also cleaned up to remove the inline ${{ inputs.website-name }} expression.

