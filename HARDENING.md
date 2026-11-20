<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ChristopheLav--iis-deploy/v1.1.1** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the composite step 'Deploy Application ... on IIS' puts GitHub Actions expressions straight into the pwsh `run:` script: `${{ github.action_path }}/scripts/PublishAspNet5Website.ps1 ${{ inputs.source-path }} ${{ inputs.msdeploy-service-url }} ${{ inputs.website-name }} ${{ inputs.msdeploy-username }} ${{ inputs.msdeploy-password }} ${{ inputs.skip-extra-files }}`. These values are substituted into the script before PowerShell parses it, so input values containing PowerShell metacharacters (e.g. `; Invoke-Expression ...`, `$(...)`) run as code. The same pattern also puts the password in the script text. Fix: map each value into an `env:` block (e.g. SOURCE_PATH: ${{ inputs.source-path }}) and pass it as a quoted argument, e.g. `& "$env:GITHUB_ACTION_PATH/scripts/PublishAspNet5Website.ps1" "$env:SOURCE_PATH" ...`.

Locations:

- `action.yml:28`
- `action.yml:29`
- `action.yml:30`
- `action.yml:31`
- `action.yml:32`
- `action.yml:33`
- `action.yml:34`
- `action.yml:35`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 920c2da..e7fbe6e 100644
--- a/action.yml
+++ b/action.yml
@@ -23,16 +23,41 @@ inputs:
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
-        ${{ inputs.skip-extra-files }}
+      env:
+        ACTION_PATH: ${{ github.action_path }}
+        SOURCE_PATH: ${{ inputs.source-path }}
+        MSDEPLOY_SERVICE_URL: ${{ inputs.msdeploy-service-url }}
+        WEBSITE_NAME: ${{ inputs.website-name }}
+        MSDEPLOY_USERNAME: ${{ inputs.msdeploy-username }}
+        MSDEPLOY_PASSWORD: ${{ inputs.msdeploy-password }}
+        SKIP_EXTRA_FILES: ${{ inputs.skip-extra-files }}
+      run: |
+        & "$env:ACTION_PATH/scripts/PublishAspNet5Website.ps1" `
+          -packOutput "$env:SOURCE_PATH" `
+          -deployUrl "$env:MSDEPLOY_SERVICE_URL" `
+          -websiteName "$env:WEBSITE_NAME" `
+          -deployUserName "$env:MSDEPLOY_USERNAME" `
+          -deployUserPassword "$env:MSDEPLOY_PASSWORD" `
+          -skipExtraFilesOnServer "$env:SKIP_EXTRA_FILES"
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

I moved every ${{ }} expression out of the pwsh run: script in action.yml and into the step's env: block. The step now calls the script as & "$env:ACTION_PATH/scripts/PublishAspNet5Website.ps1" and passes each value as a quoted, named parameter (-packOutput, -deployUrl, -websiteName, -deployUserName, -deployUserPassword, -skipExtraFilesOnServer). This is the same change the previous harden made. The password is no longer written into the script text. I also took the website-name expression out of the step name, as the previous patch did; the step is now 'Deploy Application on IIS'. skip-extra-files still reaches the script as a string, and the script already converts that to a boolean. Nothing was run to test this.

