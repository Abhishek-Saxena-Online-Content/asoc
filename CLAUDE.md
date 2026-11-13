# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Salesforce DX project (`asoc`) built from the standard `sf project generate` template. Source format metadata lives in the single package directory `force-app/main/default/` (no namespace, `sourceApiVersion` 68.0).

Current state: there is **no custom code yet** — `classes/`, `triggers/`, `lwc/`, `aura/`, etc. are empty. The only content is ~390 **standard** object definitions under `objects/` (retrieved from the org), plus 23 custom fields (`*__c`) on standard objects such as Account, Contact, Lead, Opportunity, and Case. There are no custom objects.

## Org configuration

- Default target org alias is `asoc` (set in `.sf/config.json`); `sf` commands target it unless `--target-org` is passed.
- `config/project-scratch-def.json` defines a Developer-edition scratch org if one is needed.
- `manifest/package.xml` uses wildcards for Apex classes/pages/components/triggers/test suites, Aura, LWC, and static resources only — it does **not** include objects/fields. Use `--metadata CustomObject:<Name>` / `CustomField:<Obj>.<Field>__c` or `--source-dir` to deploy or retrieve object metadata.
- `.forceignore` excludes `package.xml`, `**/__tests__/**`, `jsconfig.json`, and `.eslintrc.json` from deploys.

## Commands

Salesforce CLI (`sf`):

```bash
sf project deploy start --source-dir force-app            # deploy everything
sf project deploy start --source-dir <path-to-file-or-dir> # deploy one component
sf project retrieve start --manifest manifest/package.xml  # retrieve code metadata
sf apex run test --tests MyClassTest --result-format human --synchronous   # one Apex test class
sf apex run test --test-level RunLocalTests --wait 10      # all local Apex tests
sf apex run --file scripts/apex/hello.apex                 # anonymous Apex
sf data query --file scripts/soql/account.soql             # SOQL
sf template generate apex class --name Foo --output-dir force-app/main/default/classes
sf template generate lightning component --type lwc --name foo --output-dir force-app/main/default/lwc
```

Node tooling (run `npm install` first; `node_modules` is not yet installed):

```bash
npm run lint                  # ESLint on aura/lwc JS only
npm test                      # LWC Jest tests (sfdx-lwc-jest)
npx sfdx-lwc-jest -- path/to/__tests__/foo.test.js   # single LWC test file
npm run test:unit:watch       # watch mode
npm run prettier              # format cls/trigger/xml/html/js/etc.
npm run prettier:verify
```

## Claude Code setup (`.claude/`)

- `settings.json`: permission lists (`allow`/`ask`/`deny`) are currently **empty** — no commands or MCP tools are pre-allowed or forced to ask. Behavior depends on the session's permission mode (default mode prompts for anything not allowed; auto mode uses a classifier).
- `/deploy [path]` (skill, user-invoked only via `disable-model-invocation`): preview, then validate, then deploy (only after confirmation). `/apex-test [Class]` (skill): run Apex tests and summarize.
- `apex-reviewer` subagent: review Apex for governor limits, bulkification, sharing, and CRUD/FLS problems.
- `settings.local.json` is gitignored and holds personal overrides.

## Conventions enforced by tooling

- Husky pre-commit runs `lint-staged`: Prettier on all staged source (incl. Apex via `prettier-plugin-apex` and XML via `@prettier/plugin-xml`), ESLint on aura/lwc JS, and related LWC Jest tests with `--bail`.
- Prettier: `trailingComma: "none"`; LWC `.html` uses the `lwc` parser.
- LWC Jest tests go in `__tests__/` folders inside each component bundle (they are excluded from deploys by `.forceignore`).
