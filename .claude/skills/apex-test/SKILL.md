---
name: apex-test
description: Run Apex tests against the asoc org and summarize failures and coverage. Use when asked to run, check, or debug Apex tests.
argument-hint: "[TestClassName[.methodName] ...]"
---

Run Apex tests in the default org (`asoc`).

- With arguments: `sf apex run test --tests $ARGUMENTS --result-format human --code-coverage --synchronous`
  (`--synchronous` only works for a single class; for several, drop it and use `--wait 10`).
- Without arguments: `sf apex run test --test-level RunLocalTests --result-format human --code-coverage --wait 10`.

The tests run against the deployed code, so deploy local changes first (see `/deploy`).

Summarize: pass/fail counts, each failing method with its message and stack trace location
(map it to the file under `force-app/main/default/classes/`), and per-class coverage below 75%.
For LWC tests use `npm test` instead.
