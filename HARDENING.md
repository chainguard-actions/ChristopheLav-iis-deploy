<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ChristopheLav--iis-deploy/v1.0.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the composite step's pwsh `run:` block puts GitHub expressions straight into the script text: `${{ github.action_path }}/scripts/PublishAspNet5Website.ps1 ${{ inputs.source-path }} ${{ inputs.msdeploy-service-url }} ${{ inputs.website-name }} ${{ inputs.msdeploy-username }} ${{ inputs.msdeploy-password }}`. These values are substituted unquoted before PowerShell parses the script. A crafted input (for example one containing `; <command>`) would run arbitrary commands, and the password ends up inside the generated script text. Fix: pass each value through `env:` (e.g. SOURCE_PATH, MSDEPLOY_URL, WEBSITE_NAME, MSDEPLOY_USERNAME, MSDEPLOY_PASSWORD, plus $env:GITHUB_ACTION_PATH) and call the script as `& "$env:GITHUB_ACTION_PATH/scripts/PublishAspNet5Website.ps1" "$env:SOURCE_PATH" ...`.

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index e2d84af..c83d9d6 100644
--- a/action.yml
+++ b/action.yml
@@ -19,15 +19,39 @@ inputs:
 runs:
   using: "composite"
   steps:
-    - name: Deploy Application ${{ inputs.website-name }} on IIS
+    - name: Deploy Application on IIS
       shell: pwsh
-      run: >
-        ${{ github.action_path }}/scripts/PublishAspNet5Website.ps1
-        ${{ inputs.source-path }}
-        ${{ inputs.msdeploy-service-url }}
-        ${{ inputs.website-name }}
-        ${{ inputs.msdeploy-username }}
-        ${{ inputs.msdeploy-password }}
+      env:
+        ACTION_PATH: ${{ github.action_path }}
+        SOURCE_PATH: ${{ inputs.source-path }}
+        MSDEPLOY_SERVICE_URL: ${{ inputs.msdeploy-service-url }}
+        WEBSITE_NAME: ${{ inputs.website-name }}
+        MSDEPLOY_USERNAME: ${{ inputs.msdeploy-username }}
+        MSDEPLOY_PASSWORD: ${{ inputs.msdeploy-password }}
+      run: |
+        & "$Env:ACTION_PATH/scripts/PublishAspNet5Website.ps1" `
+          "$Env:SOURCE_PATH" `
+          "$Env:MSDEPLOY_SERVICE_URL" `
+          "$Env:WEBSITE_NAME" `
+          "$Env:MSDEPLOY_USERNAME" `
+          "$Env:MSDEPLOY_PASSWORD"
 branding:
   icon: upload-cloud
-  color: purple
\ No newline at end of file
+  color: purple

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

I fixed the script-injection problem in action.yml, which is the same change the previous harden made. Nothing was run or tested in this pass.

The pwsh step used to paste five inputs and the action path straight into its script text. Each one is now set in the step's env: block (ACTION_PATH, SOURCE_PATH, MSDEPLOY_SERVICE_URL, WEBSITE_NAME, MSDEPLOY_USERNAME, MSDEPLOY_PASSWORD), so a crafted input can no longer run commands. The password also no longer appears in the generated script text.

The step now calls the script with `& "$env:ACTION_PATH/scripts/PublishAspNet5Website.ps1"`, each argument in double quotes. That is the safe way to write it in PowerShell; a bare `$env:VAR/path` would be a parse error. The script takes five required positional parameters, and they are still passed in the same order.

I also took the input expression out of the step name, as the previous harden did. That changes what shows in the log: the step now reads "Deploy Application on IIS" without the website name.

