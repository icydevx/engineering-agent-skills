---
name: project-foundation
description: Use when starting work in a new or unfamiliar repository, or when the user asks for a full project audit, architecture or code-quality review, controlled cleanup, technical-debt assessment, or creation of project-specific engineering rules. Do not use for routine feature work or small bug fixes; use engineering-guard for those.
when_to_use: First session in a repository, inherited-code handoff, before a large refactor or migration, after a long pause, or when project rules are missing, outdated, or contradicted by the code.
---

# Project Foundation

**Core principle:** Establish an evidence-based, project-specific foundation before changing code. A broad rewrite is not quality, and an unexamined area is not a finding.

This skill is a deep, usually one-time mission. It produces a verified picture of the repository, a prioritized pre-change report, optionally a small set of controlled cleanup changes, and a durable engineering-rules file that `engineering-guard` can enforce.

## Choose the operating mode first

| Mode | When | Edits allowed |
|---|---|---|
| Audit only | User asks to review, assess, map, or diagnose; mode is unclear | No |
| Audit + controlled cleanup | User explicitly authorizes cleanup and accepts the report gate | Low-risk, justified batches only |
| Rules refresh | Project already has rules; they are stale or incomplete | Rules file only, plus approved low-risk cleanup |

If the request is ambiguous, default to **audit only** and ask before editing. "Clean up the project" authorizes investigation and a cleanup proposal, not unrestricted rewriting. High-risk changes always require item-specific approval.

## Phase 0 - Safety snapshot

Before deep reading or editing:

1. Record the current branch and commit. Run `git status --short`, `git diff --stat`, and list untracked files. Note submodules or generated directories.
2. Treat every uncommitted or untracked file as user work. Never assume it is yours, and never revert, stash, clean, checkout over, or overwrite it.
3. Run the project's existing tests and quality commands if they are discoverable. Record the exact commands, actual output, exit codes, and pre-existing failures. This is the baseline for every later comparison. Do not treat another agent's summary as a test result; if a check was delegated, independently re-run it or mark it unverified.
4. Identify files that must not be touched: vendored code, generated files, lockfiles, migration history, secrets, deployment manifests, and existing project documentation unless the user explicitly includes them.
5. Do not run any state-changing Git command, including `add`, `commit`, `merge`, `rebase`, `push`, state-changing `pull` or `fetch`, `stash`, `reset`, `clean`, `restore`, `checkout`, `switch`, changing or deleting a branch or tag, or changing `git config`, unless the user explicitly authorizes that exact operation and scope for this run. This skill itself does not perform Git writes; if a Git write is required, stop and let the user or `engineering-guard` handle it with that authorization. Read-only `status`, `diff`, `log`, and `show` are allowed. General shell permission is not permission for a Git write; decompose compound commands and check every effect.
6. Never copy a secret, token, password, connection string, or private key into notes or reports. Record only the variable name and a redacted indication.

Output a short safety snapshot before continuing: branch and commit, dirty and untracked files, baseline check results, and the do-not-touch list.

## Phase 1 - Discover the real project

Build a **coverage ledger** as you go. Use this shape:

| Area | Files or commands examined | Coverage | Notes |
|---|---|---|---|
| ... | ... | full / partial / not examined | ... |

Coverage is a claim about evidence. Never mark an area examined because you saw its directory name.

Inventory what exists and how it actually runs:

- Languages, frameworks, runtimes, package manifests, lockfiles, versions, and dependency policy.
- Folder structure and the real responsibility of each top-level area.
- Entry points, startup path, request/event/job flows, and the main data flow end to end.
- Architecture boundaries and dependency direction between layers and modules.
- Database, models, schema, migrations, relations, transactions, and every data-access path.
- APIs and contracts: routes, inputs, outputs, validation, errors, versioning, consumers.
- Authentication, authorization, trust boundaries, input validation, and secret handling.
- Background jobs, queues, schedulers, events, retries, idempotency, and async failure paths.
- Configuration and environment variables, including which are required versus optional.
- Logging, monitoring, error reporting, and how a production failure would be diagnosed.
- Tests, coverage, test isolation and repeatability, linters, formatters, static analysis, CI/CD, containers, and deployment.
- Documentation, ADRs, project maps, README, and onboarding notes.
- Coupling, duplicated logic, dead code, ambiguous ownership, and structural inconsistencies.

Rules of evidence:

- Observe before judging. Read actual code, tests, and configuration, not only filenames.
- Follow execution across files for every important path. Do not stop at the first file that looks relevant.
- Treat documentation as a claim to verify, not as ground truth. Report every material contradiction between docs and behavior.
- For a large repository, stage the audit. A partial audit is acceptable only when the coverage ledger says what was not examined and why.
- If a tool, credential, service, or environment is unavailable, record the limitation. Do not infer its output.
- Do not create new artifacts during discovery. Notes belong in the working report, not in the repository.

## Phase 2 - Assess quality and architecture

Assess at least these dimensions. For each, record findings only where there is evidence:

1. **Architecture** - Are responsibilities, module boundaries, and dependency direction coherent? Are there cycles or hidden coupling?
2. **Clean Code** - Are names meaningful, functions focused, classes coherent, and control flow understandable?
3. **SOLID and separation of concerns** - Which violations cause real change or testing pain?
4. **DRY** - Is duplicated logic genuinely shared behavior, or only superficially similar code that should stay separate?
5. **KISS and YAGNI** - Is there unnecessary complexity, premature abstraction, unused configuration, or speculative generality?
6. **Maintainability** - Can a new engineer predict where a change belongs and what else it affects?
7. **Reliability** - Are errors handled, transactions correct, concurrency safe, retries bounded, and failure boundaries clear?
8. **Security** - Is there a demonstrable flaw in validation, authorization, secret handling, or trust boundaries?
9. **Performance** - Is there evidence of a bottleneck or wasteful resource use? Speculation is not a finding.
10. **Testing** - Are important behaviors, edge cases, and regressions covered? Are tests meaningful, isolated from shared state, and repeatable, or merely present?
11. **Consistency** - Do modules follow the same contracts, patterns, and conventions?
12. **Documentation** - Does it match actual behavior and current commands?

For every finding, record:

| ID | Severity | Dimension | Evidence | Impact | Confidence | Proposed action | Change risk |
|---|---|---|---|---|---|---|---|

Use `file:line` or an exact command and observed output as evidence. Severity is about real impact, not taste:

- **Critical** - security exploit, authentication bypass, data loss or corruption, production outage, irreversible destructive behavior.
- **High** - major architecture, reliability, contract, or behavioral defect with clear user or operational impact.
- **Medium** - maintainability or quality problem with a real, recurring cost.
- **Low** - optional improvement or cosmetic issue.

Also produce:

- **Strengths to preserve** - patterns, tests, safeguards, and conventions that already work.
- **Do-not-change list** - public contracts, data formats, compatibility surfaces, and fragile areas that require a separate decision.
- **Open questions** - items that need human or domain knowledge and cannot be resolved from the repository.

Do not manufacture findings to fill a template. "No material issue found" is a valid result.

## Phase 3 - Pre-change report and approval gate

Before any edit, deliver a report with:

1. Scope and coverage ledger.
2. Current architecture and the main execution and data flows.
3. Strengths that must be preserved.
4. Confirmed issues with evidence, impact, and severity.
5. Risks and technical debt that are not yet confirmed issues.
6. Proposed changes, split into low-risk and high-risk, each with a reason and a verification plan.
7. The do-not-touch list.
8. Open questions and decisions required from the user.

Report skeleton:

```text
Scope and coverage:
Architecture and main flows:
Strengths to preserve:
Confirmed issues (ID, severity, evidence, impact):
Risks and debt:
Proposed low-risk changes:
Proposed high-risk changes:
Allowed cleanup scope (files or modules and permitted change types):
Out-of-scope changes (stop for approval):
Do not change:
Open questions:
```

Then apply the gate:

- **Audit only:** stop. Do not edit.
- **Cleanup authorized:** you may proceed only with low-risk, behavior-preserving changes that are inside the allowed scope and already justified in the report.
- **High-risk item:** stop for that item. This includes behavior changes, public API or schema changes, dependency additions or upgrades, migrations, deployment or configuration changes, security-posture changes, file moves, and deletions. Present the specific proposal and wait for explicit approval.

Before cleanup, define the allowed change scope in the report: the exact files or modules that may change and the kinds of change permitted. That scope is a hard boundary. If the work requires an out-of-scope file, file deletion or move, behavior change, public API or schema change, migration, dependency addition or upgrade, deployment or configuration change, or security-posture change, stop and get item-specific approval for that item. A general cleanup authorization does not cover any of these.

A general instruction to "clean up" is not approval for high-risk items. Silence is not approval.

## Phase 4 - Controlled cleanup

When cleanup is authorized, change in small, independently verifiable batches. One batch should have one concern and be reviewable as a unit.

For each batch:

1. Confirm the relevant baseline test result from Phase 0.
2. If coverage is too weak to detect regressions, add characterization tests where feasible. If that is not feasible, record the gap and reduce the change scope.
3. Make the minimal change that achieves the approved outcome.
4. Run the project's documented test command from its documented working directory, starting with the narrowest relevant tests and then the broader suite when practical. Inspect the actual output and exit code, not just a summary. If that command would modify user data or shared state, reproduce it safely in an isolated copy, but verify the canonical command's result in a state equivalent to the project's real state; state exactly what was isolated and what limitation remains. A test that passes only in a clean temporary directory does not prove the canonical suite passes. If a check was not run, or was only reported by a subagent or another agent, do not mark it passed; independently re-run it or report it as unverified. Claim independent execution only when the verifying command was actually run independently in the relevant state. Report any deviation.
5. Inspect the diff. Confirm only approved files changed and no user work was altered.
6. Record the result. Stop and report if a regression appears or a result is uncertain.

Cleanup constraints:

- Preserve current behavior and public contracts unless the report explicitly approved a change.
- Choose the smallest behavior change that satisfies the proven contract. Do not add append, merge, migration, legacy-format, or compatibility semantics that no test, caller, or approved requirement demands.
- Do not delete, weaken, skip, or rewrite a test merely to make the suite pass. If a test is wrong, prove why and treat the change as a decision requiring approval.
- Do not add a dependency, change a schema, run a destructive migration, alter a public API, or change deployment behavior without explicit approval.
- Treat the approved cleanup scope as a hard boundary. If the work requires an out-of-scope file, file deletion or move, behavior change, public API or schema change, migration, dependency addition or upgrade, deployment or configuration change, or security-posture change, stop and get item-specific approval before making it. General cleanup authorization does not cover these.
- Do not move files for aesthetics. Trace imports, dynamic loading, path assumptions, build scripts, CI, and deployment references first.
- Do not create a new Service, Repository, Interface, Factory, Manager, or abstraction layer merely to follow a pattern. Add it only when the project has a proven need and the change is approved.
- Do not delete a file because it "looks unused." Search imports, string references, configuration, templates, CLI entry points, tests, docs, and dynamic loading. If usage cannot be ruled out, leave it and report it.
- Do not reformat or clean unrelated files. Keep the diff limited to the approved scope.
- If a test was already failing before the change, record it as a pre-existing failure and compare results against the baseline. Do not hide it or fix it silently.
- If any batch becomes larger or riskier than expected, stop and re-scope.

## Phase 5 - Define project-specific engineering rules

Create or refresh the project's policy file. Default path: `ENGINEERING_RULES.md` at the repository root, unless the project already has an established equivalent. Never overwrite an existing rules file silently; read it, preserve valid rules, and propose material rewrites before applying them.

The rules file must be specific to this repository, not a generic template. Reference real paths, commands, patterns, and known exceptions.

Required sections (mark a section **N/A** with a reason if it genuinely does not apply):

1. Purpose, scope, owner, date, and source of truth.
2. How to use the file, precedence, and conflict resolution.
3. Approved architecture and the responsibility of each layer or module.
4. Folder structure and naming conventions.
5. Clean Code boundaries for functions, classes, names, and comments.
6. Allowed and forbidden patterns.
7. Validation, error handling, and failure reporting.
8. Database access, transactions, migrations, and data ownership.
9. Security, authorization, input trust boundaries, and secret handling.
10. Testing standards, test naming, required coverage of important behavior, test isolation and repeatability, and how to run tests.
11. Formatting, linting, static analysis, build, and the exact commands.
12. API contracts, versioning, and backward compatibility.
13. Dependency policy and when a new abstraction is justified.
14. Logging, background jobs, queues, schedulers, retries, and idempotency, if present.
15. Documentation and changelog update policy.
16. Git and change-management policy, including prohibited operations.
17. Pre-change and post-change review process.
18. Known deviations, owners, expiry or review dates, and the waiver process.

Write every important rule in an evaluable form. Use a table or a repeated block:

| Rule ID | Rule | Why | Applies to | Violation looks like | How to detect | Required action |
|---|---|---|---|---|---|---|

Requirements for the rule set:

- A rule must state a concrete, observable requirement. "Write clean code" is not a rule; "Public functions validate untrusted input at the boundary before use" is.
- Every rule must be detectable by a test, linter, static analysis, code review, or a named command. If it cannot be checked, label it as a preference rather than a mandatory rule.
- Separate **mandatory** rules from **preferences**. Do not present taste as a hard requirement.
- Define conflict precedence. Default order: safety, security, and data integrity first; then public contracts and compatibility; then approved architecture; then maintainability; then style. If two mandatory rules still conflict, stop, document the conflict, and require a human decision.
- Include known exceptions with an owner and review date. An unowned permanent exception is a rule that no longer exists.
- Make the rules consistent with what the code actually does. If the code is wrong, record the gap and the remediation plan; do not write a rule that pretends the gap does not exist.
- Never include secrets, credentials, or sensitive values in the rules file.

After writing the file, validate it:

- Every required topic is covered or explicitly N/A.
- Every mandatory rule has a detection method and a required action.
- Referenced commands exist and were run, or are marked unverified with a reason.
- The rules do not contradict each other or the approved architecture.
- The file contains no secrets and no generic filler that could apply unchanged to any repository.
- The location is recorded so `engineering-guard` can find it. If there is no reliable discovery path, recommend a short pointer in the project's `AGENTS.md`; do not edit `AGENTS.md` without permission.

## Phase 6 - Final report

Report honestly, including what you did not do:

- **Coverage** - what was examined in full, partially, and not at all, with reasons.
- **Changes** - files changed, batches completed, and behavior preserved.
- **Remaining issues** - what was not fixed and why, including deferred high-risk items.
- **Verification** - exact commands run, actual output and exit codes, baseline versus final results, any pre-existing failures, and any check that was not independently run.
- **Limitations** - unavailable tools, missing credentials, unexamined areas, and unverified assumptions.
- **Rules** - the exact rules-file path and a short summary of its mandatory sections.
- **Human decisions** - approvals still needed and open questions.
- **Safety confirmation** - no destructive Git operation, no lost user work, no secret exposed.

Never report an unexamined area as clean, an unrun or independently unverified test as passing, or an unapproved change as completed.

## Red flags - stop and re-check

| Thought | Reality |
|---|---|
| "The project is small, so discovery can be skipped." | Small projects still have hidden coupling and undocumented contracts. |
| "The README says how it works." | Documentation is a claim. Verify it against code and tests. |
| "Tests pass, so the architecture is fine." | Tests cover behavior, not boundaries, coupling, or maintainability. |
| "This file looks unused, so delete it." | Usage can be dynamic, configured, or external. Trace it or leave it. |
| "The user said clean up, so I can change anything." | Cleanup authorization is scoped by risk and the report gate. |
| "I will refactor first and report later." | The pre-change report is mandatory before edits. |
| "I will update the README to match the code." | Documentation changes need authorization and verified behavior. |
| "There are no tests, but the change is obvious." | Obvious changes still regress. Add characterization coverage or reduce scope. |
| "I will stash or reset to get a clean baseline." | Never destroy or hide user work to simplify the audit. |
| "I will include the env values so the report is complete." | Secrets are never evidence. Report variable names only. |
| "One large cleanup is faster." | Large batches hide regressions and make review meaningless. |
| "The test passes in a clean temp directory, so the canonical command is fine." | State-dependent tests can fail from the project root. Verify the documented invocation and real starting state. |
| "Adding append or legacy compatibility is harmless robustness." | Extra semantics change the contract and can make tests order-dependent. |
| "The rules template is good enough." | A generic rule set does not control this project's actual risks. |

## Relationship to engineering-guard

This skill builds the foundation and writes the rules. It does not supervise every later change. After the foundation is in place, use `engineering-guard` for feature work, bug fixes, refactors, schema changes, and reviews so the rules are enforced continuously.
