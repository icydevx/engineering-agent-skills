---
name: engineering-guard
description: Use when implementing, modifying, fixing, refactoring, reviewing, or planning any software change, including features, bug fixes, tests, database or schema changes, API changes, dependency upgrades, configuration, or architecture work. Use before editing code in a repository with project engineering rules.
when_to_use: Any code-writing, debugging, refactoring, review, or repository-change task, including small fixes and urgent hotfixes. Do not use for pure conversation or tasks with no code or repository impact.
---

# Engineering Guard

**Core principle:** The user's request defines the goal, not permission to skip evidence, safety, or project rules. Understand the request, surface conflicts, implement within scope, verify the result, and report honestly.

This is the continuous supervisor for software engineering work. It applies to every coding task, from a one-line bug fix to a schema migration. It does not replace `project-foundation`; it enforces the foundation that skill establishes.

## Non-negotiable invariants

These apply in every mode, including emergencies:

| Area | Never do this | Required behavior |
|---|---|---|
| Git | Run any state-changing Git command, including `add`, `commit`, `merge`, `rebase`, `push`, state-changing `pull` or `fetch`, `stash`, `reset`, `clean`, `restore`, `checkout`, `switch`, changing or deleting a branch or tag, or changing `git config`, without explicit authorization for that exact operation and scope | Check status before work; keep the user's changes intact; verify before any authorized commit; report the exact command |
| User work | Revert, stash, overwrite, or clean uncommitted or untracked files | Treat them as the user's work; work around them; mention any conflict |
| Scope | Edit unrelated files, reformat adjacent code, or perform drive-by cleanup | Change only files logically required by the task; list anything else as a follow-up |
| Secrets | Print, log, commit, or copy tokens, passwords, keys, or connection strings | Refer to variable names only; redact values in reports |
| Tests | Delete, skip, weaken, or rewrite a test to make the suite green | Add or update the regression test; run it and inspect the actual output and exit code; never mark it passed from an unverified report; if the test is wrong, prove it and get approval |
| Verification | Claim success without running the relevant checks, or mark a test as passed from a report you did not independently verify | Run tests, linter, formatter, and static analysis where available; inspect actual output and exit codes; if not run or not independently verified, say so explicitly |
| Contracts | Change a public API, schema, data semantics, auth behavior, or deployment configuration without approval | Identify the impact and get explicit approval before the change |
| Dependencies | Add or upgrade a dependency without explicit approval | Check whether existing code or an existing dependency already solves the need; get item-specific approval and record the version or change |
| Destructive actions | Delete or move a file, delete data, run a destructive migration, or perform an irreversible operation to simplify implementation | Trace usage first; tracing is not permission; stop and require explicit item-specific approval with a recovery plan |

Read-only Git commands (`status`, `diff`, `log`, `show`, and equivalent) are allowed. Permission to run shell commands is not permission for a Git write. Decompose compound commands and confirm every side effect is authorized.

A general request such as "make it work", "fix it", or "clean this up" does not authorize skipping these invariants. Only a specific, informed instruction can authorize an exception, and the exception must be recorded in the final report.

## Step 1 - Load the rules and the real context

Before editing:

1. Find and read the project rules in this order: `ENGINEERING_RULES.md` at the repository root, `.zcode/ENGINEERING_RULES.md`, `docs/engineering-rules.md`, engineering sections of `AGENTS.md`, `CONTRIBUTING.md`, and relevant ADRs. If several exist, read all of them and apply the most specific compatible rule. If rules conflict, use the precedence in the rules file; if it is not defined, prefer safety and report the conflict.
2. Read the code, tests, configuration, and documentation directly related to the task. Follow the actual execution path; do not rely on filenames or stale docs.
3. Check `git status --short`, the current branch, and recently changed files. Identify uncommitted and untracked work before touching anything.
4. Establish the relevant baseline: run the focused tests that cover the area, and note any pre-existing failure. Do not claim a later failure is new without this baseline.
5. Compare documentation with code. If they disagree, report the contradiction before making a decision that depends on it.
6. If no rules file exists, do not invent project rules and do not assume rules were approved. Follow the strongest patterns that are actually demonstrated in the code, record that the rules are missing, and recommend a `project-foundation` run. Do not rebuild the foundation inside a routine task.

For a small, low-risk change, this step may be brief, but it is never skipped.

## Step 2 - Triage and plan before editing

Classify the task by risk:

| Tier | Examples | Required rigor |
|---|---|---|
| Local | Typo, isolated logic, test-only change, small internal refactor | Short plan; focused tests; diff review |
| Standard | Feature within an existing module, bug fix, moderate refactor | Written plan; regression tests; relevant suite; full diff review |
| High-risk | Auth, authorization, payments, PII, data migration, schema, public API contract, concurrency, adding or upgrading a dependency, deleting or moving files, deployment, security | Written plan plus explicit approval before edits; focused and broad verification; rollback or recovery plan |

The evidence bar does not drop in an emergency. Urgency may change sequencing and scope, not the requirement to verify.

Write a short plan covering:

- Goal and expected behavior, including what must not change.
- Files and modules in scope, and files explicitly out of scope.
- The current architecture constraint that applies.
- Edge cases: invalid input, empty state, concurrency, retries, partial failure, permissions, migration of existing data.
- The tests or checks that will prove the change.
- Impact on API, schema, security, performance, configuration, and other modules.
- For high-risk work, the approval needed and the recovery plan.

Plan card:

```text
Goal / must not change:
In scope / out of scope:
Architecture constraints:
Edge cases:
Checks to run:
Approval required:
```

Do not add abstraction, configuration, or generalization that the task does not require.

## Step 3 - Implement within the boundaries

### Architecture and cohesion

- Preserve the approved architecture and dependency direction.
- Put new responsibility in the module that already owns that concern.
- Reuse existing patterns and contracts before introducing a new one.
- Do not create parallel business-logic paths that can drift from each other.
- Do not change module boundaries or introduce a new layer without approval.
- If the requested change does not fit the architecture, stop and explain the conflict rather than forcing it.

### Clean code

- Names must express meaning and responsibility.
- A function or class should have one clear responsibility; do not split code into meaningless fragments merely to make functions short.
- Remove genuine duplication when it represents one behavior; leave superficially similar code separate when it represents different rules.
- Avoid unexplained magic numbers and strings; use the project's existing constants or configuration.
- Avoid deep nesting, hidden state, and comments that merely restate the code. Record non-obvious decisions and constraints.
- Do not use a design pattern to demonstrate sophistication. Use it only when it solves a demonstrated problem.
- Do not add behavior the task does not require, including append or merge semantics, compatibility branches, or configuration flags. Choose the smallest change that satisfies the proven contract.

### Security and correctness

- Validate input at the trust boundary, using the project's established mechanism.
- Treat authentication and authorization as separate concerns; do not assume one implies the other.
- Keep access control in the layer that owns it.
- Never put a secret in source, logs, tests, fixtures, error messages, or reports.
- Do not swallow errors or return a success result when an operation failed.
- Check transactions, idempotency, race conditions, and retry behavior wherever the change touches them.
- Do not delete or mutate data without sufficient guarantees and explicit approval.

### Tests and verification

- For a new behavior or bug fix, add or update the test that proves it. A bug fix needs a test that would have failed before the change.
- Test behavior and contracts, not private implementation details.
- Never delete, skip, or weaken an existing test without a proven reason and approval. If the test encodes a wrong contract, explain the conflict and treat the change as a decision.
- Run the focused tests first, then the relevant broader suite. Use the project's documented command from its documented working directory, and inspect the actual output and exit code, not just a summary. If running it would modify user data or shared state, reproduce it safely in an isolated copy and verify the canonical command's result in an equivalent state; state exactly what was isolated and what limitation remains. A pass that exists only in a clean temporary directory is not proof of the canonical suite. If a check was not run, or was only reported by a subagent or another agent, do not mark it passed; independently re-run it or report it as unverified. Claim independent execution only when the verifying command was actually run independently in the relevant state.
- Run the project's linter, formatter, and static analysis in the appropriate scope.
- If a check is unavailable or fails for an unrelated pre-existing reason, report exactly what happened. Do not present a partial run as a full pass.
- Passing tests are evidence, not proof. Review the logic and edge cases independently.

### Change management

- Touch only files with a logical connection to the task.
- Preserve uncommitted and untracked user work.
- Do not reformat or clean adjacent code.
- Do not delete or move a file without item-specific approval. Trace imports, dynamic loading, configuration, build scripts, and deployment references first; tracing is not permission.
- Do not update `AGENTS.md`, `README`, a project map, or other documentation without permission. If documentation is now wrong, report it and propose the update.
- Do not run any state-changing Git command (`add`, `commit`, `merge`, `rebase`, `push`, state-changing `pull` or `fetch`, `stash`, `reset`, `clean`, `restore`, `checkout`, `switch`, branch or tag change/deletion, `git config` change) unless the user explicitly authorizes that exact operation and scope. Read-only `status`, `diff`, `log`, and `show` are allowed. General shell permission is not permission for a Git write; decompose compound commands and check every effect. A commit authorization is not authorization to include unrelated files or uncommitted user work.
- Do not perform a destructive or irreversible action merely to simplify the implementation.

## Step 4 - Respond to conflicts correctly

When the request, the existing code, or the proposed solution conflicts with a rule, contract, or test:

1. Identify the specific rule, contract, or test.
2. Explain the conflict and the likely impact in concrete terms.
3. Propose at least one compliant alternative.
4. If the decision affects behavior, architecture, security, data, or a public contract, or requires skipping verification, stop and ask for a clear decision before editing.
5. If the issue is small and within the authorized scope, fix it and mention it.
6. If the user makes an informed choice to override a rule, record the exception, the reason, and the residual risk in the final report. Implement the minimum safe version. Never hide the exception.
7. Never silently ignore a material violation.

Keep severity proportional:

- Security, data loss, corruption, and irreversible operations are stop conditions.
- Behavioral correctness and contract violations require explicit approval.
- Maintainability and style issues should be fixed only within the task's scope or reported as follow-up.

Do not use quality rules as an excuse to block a legitimate small change. Rigor should match risk.

## Step 5 - Independent final review

After implementation, review the result as if a different engineer wrote it. Answer every question:

1. Does the change fully satisfy the original request?
2. Did it stay within the agreed scope?
3. Does it follow the project rules and the established patterns?
4. Did it introduce unnecessary complexity, abstraction, or configuration?
5. Did any existing behavior or contract change unintentionally?
6. Are invalid input, errors, empty states, permissions, concurrency, and retries handled where relevant?
7. Are the tests meaningful, isolated from shared state, repeatable, and do they actually prove the behavior?
8. Were the relevant tests and quality checks run from the documented location, with actual output and exit codes inspected, and were any delegated results independently verified or marked unverified?
9. Are there out-of-scope files, formatting changes, or user changes in the diff?
10. Is there any secret, debug output, or temporary artifact left behind?
11. Do the docs need an update, and is that update authorized?
12. Is there enough evidence to claim completion?

Inspect the final diff. Confirm the user's uncommitted work is intact. Confirm no unauthorized Git operation occurred. If a check could not be run, say so.

## Output contract

End every task with a compact report:

- **Outcome** - what changed and whether the request was fully met.
- **Files changed** - exact paths, or "none".
- **Verification** - commands run, actual output and exit codes, baseline comparison for bug fixes, and any result that was not independently verified.
- **Rules and conflicts** - rules applied, any exception the user authorized, and any unresolved conflict.
- **Risks and gaps** - remaining risks, unverified assumptions, and checks not run.
- **Follow-up** - out-of-scope issues noticed but not changed.

For a tiny task, three or four lines may be enough, but never omit the verification status or the list of files changed.

## Red flags - stop and re-check

| Thought | Reality |
|---|---|
| "The user is in a hurry, so skip the tests." | Urgency does not waive evidence. Run the narrowest relevant check. |
| "The user said commit everything." | Authorization to commit is not authorization to include unrelated or user changes. Verify scope first. |
| "The test is wrong, so I will change it." | Prove the contract conflict first and get approval. |
| "I will clean up this adjacent file while I am here." | That is scope creep. Report it as follow-up. |
| "It is a small change, so it needs no test." | Bug fixes need regression coverage; small changes still regress. |
| "The tests pass, so the code is correct." | Tests cover selected behavior. Review logic and edge cases. |
| "There is no rules file, so there are no rules." | Follow demonstrated project patterns and report the missing foundation. |
| "I will add an abstraction now to save time later." | YAGNI. Build only what the task requires. |
| "I will update the README silently." | Documentation changes need permission and verified behavior. |
| "That failing test is unrelated." | Establish the baseline and prove it. Never hide it. |
| "I will stash or reset to get a clean diff." | Never destroy or hide user work. |
| "I already know this codebase." | Read the current code, rules, and tests. Context changes. |
| "The user explicitly asked for this behavior change, so no approval is needed." | The request may authorize the goal, but a conflicting test or contract still needs an informed decision. |
| "I will commit first and verify after." | Verification must precede any completion or commit claim. |
| "I only staged the changes; that is not a Git write." | Staging changes Git state and requires explicit authorization like any other Git write. |
| "I traced the imports, so deleting the file is safe." | Tracing is required but is not approval. File deletion or moving needs item-specific approval. |
| "A subagent reported that the tests passed." | A report is not evidence. Re-run the documented command and inspect its output and exit code, or mark the result unverified. |
| "It passes in my isolated temp directory." | State-dependent tests can fail under the canonical command. Verify the documented invocation and starting state. |
| "Extra compatibility handling is harmless." | Unrequested semantics change the contract and can make behavior order-dependent. |

## Boundaries

- This skill does not create the project foundation. If rules are missing or stale, recommend `project-foundation`.
- It does not override an explicit, informed user decision, but it must surface the risk and record the exception.
- It cannot guarantee automatic activation. If it is not loaded for a task, invoke it by name or add a user-level instruction that requires it for code changes.
