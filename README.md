# ReleaseSentinel

**An Agentia-powered Salesforce release preflight and risk assistant**  
Catch risky changes before they reach production.

## Overview
ReleaseSentinel is a proposed Agentia CLI extension that runs read-only preflight checks on a Salesforce release package, prioritizes risks, and produces a concise, evidence-linked report before promotion.

## Why this problem matters
Salesforce releases may be delayed by malformed manifests, destructive changes, missing test evidence, and inconsistent manual checks. ReleaseSentinel gives developers one repeatable preflight report with actionable next steps.

## Proposed workflow
1. Read a supplied `package.xml` and optional evidence files.
2. Run deterministic, configurable rules.
3. Pass structured findings to an Agentia-supported agent flow for a grounded explanation.
4. Write Markdown and JSON reports.
5. Keep all deployment and approval decisions with the human release owner.

## Proposed command UX
These are **proposed command shapes**, not existing built-in Agentia commands. Adapt them to the extension APIs supported by the current hackathon environment.

```bash
agentia releasesentinel scan --manifest manifest/package.xml
agentia releasesentinel scan --manifest manifest/package.xml --policy policies/default.json --format markdown
agentia releasesentinel explain --report reports/preflight.json
```

## MVP checks
- Manifest exists and parses.
- Inventory metadata members.
- Flag presence of `destructiveChanges.xml`.
- Flag missing test/deployment evidence when required by policy.
- Emit severity, evidence, remediation, and stable finding IDs.
- Generate `reports/preflight.md` and `reports/preflight.json`.

## Example policy
```json
{
  "require_test_evidence": true,
  "flag_destructive_changes": true,
  "fail_on_severity": "BLOCKER"
}
```

## Suggested output
```text
ReleaseSentinel — Preflight result
Overall status: REVIEW REQUIRED
Risk: HIGH

[HIGH] Destructive changes detected
Evidence: destructiveChanges.xml is present.
Action: Confirm target org, backup/restore plan, and approval before promotion.

[MEDIUM] Test evidence not supplied
Evidence: No test-results file was provided to the scanner.
Action: Attach CI test summary or run the team's required validation suite.
```

## Architecture
- **CLI adapter:** command registration, arguments, help, exit codes.
- **Input adapter:** manifest and optional evidence files.
- **Rules engine:** deterministic checks and versioned policies.
- **Agentia orchestration:** explains structured findings without inventing evidence.
- **Report writer:** Markdown + JSON output.
- **CI integration:** start non-blocking; optionally fail on configured severity.

## Safety principles
- Read-only by default.
- No deployment, deletion, or approval actions.
- AI explanations must cite findings produced by the rules engine.
- Do not send source contents to an AI service unless approved.
- A clean report is not a guarantee that deployment will succeed.

## Demo plan
1. Run against a clean fixture.
2. Run against a fixture containing `destructiveChanges.xml`.
3. Show the findings and Agentia explanation.
4. Open both report formats.
5. Show how policy settings affect the result.

## Build checklist
- [ ] Confirm official Agentia CLI extension mechanism and SDK.
- [ ] Register a command using supported extension APIs.
- [ ] Implement manifest parser and configurable rules.
- [ ] Add unit tests for clean, malformed, and risky fixtures.
- [ ] Add grounded Agentia explanation step.
- [ ] Generate Markdown and JSON reports.
- [ ] Record a 3–5 minute demo.

## Hackathon pitch
ReleaseSentinel helps Salesforce teams move from “deployment failed—now what?” to “here are the risks, evidence, and next actions before promotion.” It combines transparent checks with Agentia-powered explanations while leaving release decisions under human control.
