# AI Agent Development Guide

A practical workflow guide for agents doing repo, code, config, test, release, or implementation work.

Use this guide with:

- `AI_AGENT_FIELD_GUIDE.md`
- `AI_AGENT_ROLE_GUIDE.md`
- `AI_AGENT_CODE_REVIEW_GUIDE.md`
- project-specific instructions

---

## Purpose

This guide describes how agents should plan, implement, test, debug, and hand off development work.

Development work should be:

- scoped
- reviewable
- grounded in the actual repo
- consistent with existing patterns
- tested where practical
- honest about risk
- easy to roll back

---

## Before Starting

Identify:

- active role
- task goal
- repo or project
- current branch
- relevant files
- constraints
- expected output
- safety risks
- approval gates

For non-trivial work, do not jump straight into code.

Start by understanding the current state.

---

## Local Truth First

For codebase-specific work, the local repo is the primary source of truth.

Before editing:

- inspect relevant files
- search for existing patterns
- check project instructions
- check nearby tests
- check config and build files
- identify existing helpers
- identify likely side effects

Do not plan implementation from memory alone.

Use external search for changing or external facts, such as:

- dependency docs
- API behavior
- CVEs
- platform packaging rules
- framework changes
- library compatibility
- security-sensitive claims

---

## Planning Pattern

For non-trivial development work, use this planning shape:

```text
Goal:
- [What we are trying to accomplish]

Current State:
- [What exists now]

Constraints:
- [What must not change]

Affected Areas:
- [Files/modules/components]

Plan:
1. [Step]
2. [Step]
3. [Step]

Acceptance Criteria:
- [How we know it is done]

Validation:
- [Tests/checks/manual verification]

Risks:
- [Known concerns]

Rollback:
- [How to undo safely]
```

Keep the plan short enough to execute.

---

## Scope Control

Keep diffs small.

Avoid:

- unrelated formatting changes
- broad rewrites
- speculative abstractions
- new dependencies without approval
- mixing refactor and feature work
- touching files outside the task
- changing public behavior silently

If a task needs multiple phases, split it.

Good phases:

1. behavior-preserving refactor
2. feature or bug fix
3. tests
4. docs
5. cleanup

---

## Acceptance Gates

Ask HI before:

- changing architecture
- deleting files
- force-pushing
- dropping data
- changing schemas
- changing public APIs
- changing auth or permissions
- adding major dependencies
- touching production config
- running destructive commands
- making broad automated rewrites

For destructive commands, provide:

- exact command
- working directory
- expected effect
- recovery path
- failure risk

---

## Implementation Rules

While coding:

- follow nearby style
- use existing helpers
- preserve behavior unless asked to change it
- prefer direct code over clever code
- use named constants for meaningful values
- keep functions focused
- keep errors clear
- avoid hidden global state
- avoid debug leftovers
- avoid fake placeholders
- update docs when behavior changes

Do not leave generated junk behind.

Remove:

- unused imports
- unused functions
- commented-out code
- temporary print/log statements
- sample credentials
- dead branches
- exploratory files
- one-off debug scripts unless requested

---

## Dependency Rules

Before adding a dependency, ask:

- Can the standard library do this?
- Can an existing dependency do this?
- Is the new dependency maintained?
- Is it appropriate for the project size?
- Does it affect packaging?
- Does it create supply-chain risk?
- Is HI approval needed?

Small tasks should not usually need new dependencies.

---

## Configuration Rules

Avoid hardcoded local assumptions.

Watch for:

- usernames
- local paths
- ports
- URLs
- credentials
- model names
- machine-specific values
- platform-specific commands

Prefer:

- config files
- environment variables
- documented defaults
- existing project config patterns

If a value is intentionally hardcoded, explain why.

---

## Error Handling

Good error handling:

- preserves useful context
- gives safe user-facing messages
- logs enough for debugging
- avoids leaking secrets
- avoids swallowing failures
- has bounded retry behavior
- leaves state recoverable

Avoid:

- `except Exception: pass`
- vague errors
- raw stack traces to users
- infinite retries
- misleading success states
- logging sensitive data

Ask:

- What happens when this fails?
- Who sees the error?
- Can they act on it?
- Is the system left in a safe state?

---

## Testing

Use the project’s existing test flow.

Common checks:

- syntax check
- unit tests
- integration tests
- linting
- type checks
- build command
- manual UI or CLI verification

Report exact commands and results.

Do not claim tests passed unless they actually ran.

If testing is not possible, say why.

---

## Test-Driven Generation

When behavior is clear, prefer tests as specification.

Useful flow:

1. define expected behavior
2. write or identify failing test
3. implement smallest fix
4. run targeted tests
5. run broader checks if practical
6. report results

Be careful with tests written after implementation. They can accidentally validate the implementation instead of the intended behavior.

---

## UI and Visual Work

For UI work:

- ask for screenshot or target when needed
- inspect existing UI patterns
- avoid major redesign unless requested
- check empty states
- check loading states
- check error states
- check spacing and grouping
- check keyboard/focus behavior when relevant
- provide screenshot or manual verification when possible

Expect iteration.

Do not assume the first visual pass is final.

---

## Documentation During Development

Update docs when behavior changes.

Docs may need updates when changing:

- commands
- install steps
- config
- environment variables
- public APIs
- UI flows
- permissions
- defaults
- troubleshooting steps

Follow `AI_AGENT_DOC_STYLE_GUIDE.md`.

Do not duplicate the style guide.

---

## Debugging

Debug with evidence.

Collect:

- exact command
- exact error
- logs
- config
- environment details
- reproduction steps
- expected behavior
- actual behavior

Avoid guessing from vibes.

Form a hypothesis, test it, update the hypothesis.

---

## Debugging Spiral Recovery

If debugging starts looping:

1. stop
2. summarize what was tried
3. list what is known
4. list what is unknown
5. return to a known-good state
6. apply one change at a time
7. verify each change

Do not keep stacking patches onto an unverified state.

---

## Revert to Known Good

When a change breaks the system and the cause is unclear:

- stop adding changes
- identify last known-good state
- preserve logs or failing output
- revert the smallest risky change
- re-run checks
- continue from a clean baseline

Prefer reversible changes.

---

## Security-Sensitive Development

Treat these areas as elevated risk:

- authentication
- authorization
- permissions
- cryptography
- secrets
- cookies
- sessions
- tokens
- input validation
- output encoding
- file upload/download
- path handling
- shell execution
- SQL/query construction
- deserialization
- CORS
- dependency updates
- logs containing sensitive data

Rules:

- use existing security patterns
- do not invent crypto
- do not invent auth
- validate input on trusted side
- avoid string-built shell commands
- avoid raw SQL with user input
- avoid broad CORS unless justified
- do not expose secrets in logs
- request HI review before merge

---

## Commit Management

When managing commits:

- inspect `git status`
- separate unrelated work
- avoid committing generated junk
- avoid committing secrets
- avoid committing local config
- use clear commit messages
- include tests/docs when relevant
- do not force-push without explicit approval

Suggested commit message style:

```text
area: short imperative summary

Optional body:
- what changed
- why
- tests run
```

Example:

```text
search: add dark mode toggle for results page
```

---

## Handoff Format

When handing development work back, use:

```text
Completed:
- [summary]

Files changed:
- [file]: [why]

Checks run:
- [command]: [result]

Not tested:
- [honest gaps]

Risks / limitations:
- [known concerns]

Recommended next step:
- [one concrete action]
```

---

## Common Development Failure Modes

Watch for:

- scope creep
- over-engineering
- cargo-cult patterns
- unnecessary dependencies
- hardcoded local assumptions
- debug leftovers
- dead code
- fake tests
- stale docs
- broad exception swallowing
- unreviewable diffs
- silent behavior changes
- unsupported claims of success

Use `AI_AGENT_CODE_REVIEW_GUIDE.md` before presenting non-trivial work.

---

## Quick Reference

- Read the repo first.
- Keep changes small.
- Ask before risky work.
- Follow nearby patterns.
- Avoid speculative architecture.
- Test when practical.
- Report what was not tested.
- Update docs when behavior changes.
- Stop debugging spirals early.
- Leave clean handoff notes.
