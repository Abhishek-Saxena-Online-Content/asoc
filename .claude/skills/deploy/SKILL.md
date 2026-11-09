---
name: deploy
description: Validate and deploy metadata from force-app to the asoc org. Use when asked to deploy, push, or validate Salesforce changes.
argument-hint: "[path-to-file-or-dir]"
disable-model-invocation: true
---

Deploy `$ARGUMENTS` (default: `force-app`) to the default org (`asoc`, see `.sf/config.json`).

1. Run `npm run prettier:verify` and `npm run lint` if JS or Apex changed; stop and report failures.
2. Preview: `sf project deploy preview --source-dir <path>` and show what will change.
3. Validate first: `sf project deploy validate --source-dir <path> --test-level RunLocalTests --wait 30`.
   If the path contains no Apex, use `--test-level NoTestRun`.
4. Only after validation passes, and the user confirms, run
   `sf project deploy start --source-dir <path> --wait 30`.
5. Report the deploy ID, component counts, and any failures with file and line.

Notes:
- `manifest/package.xml` does not cover objects or fields; always deploy by `--source-dir` or `--metadata`.
- `__tests__/` folders are excluded via `.forceignore`; that's expected.
