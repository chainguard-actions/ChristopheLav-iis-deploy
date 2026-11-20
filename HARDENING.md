<!-- markdownlint-disable -->

# Hardening Report: ChristopheLav--iis-deploy/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ChristopheLav--iis-deploy/v1.1.0** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is violated. The composite step 'Deploy Application on IIS' puts GitHub Actions expressions straight into its pwsh run: block: `${{ github.action_path }}/scripts/PublishAspNet5Website.ps1 ${{ inputs.source-path }} ${{ inputs.msdeploy-service-url }} ${{ inputs.website-name }} ${{ inputs.msdeploy-username }} ${{ inputs.msdeploy-password }} ${{ inputs.skip-extra-files }}`. The values are inserted into the script text before PowerShell parses it. An input that contains PowerShell metacharacters (e.g. `; malicious-command`) will therefore run as code. Fix: map each value to an env: variable (e.g. SOURCE_PATH: ${{ inputs.source-path }}). Then pass each one as a quoted argument, for example "$env:SOURCE_PATH", and call the script with & "$env:GITHUB_ACTION_PATH/scripts/PublishAspNet5Website.ps1".

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 920c2da..23b73ff 100644
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
+          -skipExtraFilesOnServer ([System.Convert]::ToBoolean("$env:SKIP_EXTRA_FILES"))
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

I re-applied the previous harden to action.yml. Every ${{ }} expression in the pwsh run: block, plus github.action_path, now goes through env: variables. The script is called with & "$env:ACTION_PATH/scripts/PublishAspNet5Website.ps1", and each argument is passed by name and double-quoted. skip-extra-files is turned into a real boolean with [System.Convert]::ToBoolean, because the script parameter is typed [bool]. I took the input expression out of the step name too. Not tested: I haven't run the action, and the tests/ files were not changed.

