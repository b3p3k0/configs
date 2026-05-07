# AI Agent Code Review Guide

A review checklist for AI-assisted development.

Use this guide before presenting non-trivial code, reviewing another agent’s work, or preparing a change for HI review.

The goal is not perfection. The goal is to protect long-term code health by catching hidden bugs, fragile assumptions, security risks, unnecessary complexity, and unreviewable churn.

---

## Review Standard

A change is acceptable when it clearly improves the project without adding unreasonable risk.

Good agent-generated code is:

- scoped
- explainable
- consistent with nearby code
- easy to review
- tested where practical
- honest about limits
- safe by default
- no more complex than necessary

Do not approve or present work just because it compiles.

Do not reject work only because it is imperfect.

Focus on whether the change moves the project in the right direction without damaging maintainability.

---

## How To Use This Guide

For small changes, run the quick checklist.

For non-trivial changes, run the full checklist.

For security-sensitive changes, run the security checklist even if the code change is small.

For large or risky changes, stop after review and ask HI before implementation or merge.

---

## Quick Checklist

Before presenting work, verify:

- [ ] The task is restated correctly.
- [ ] The changed files match the task.
- [ ] Every changed file has a clear reason.
- [ ] The change follows nearby code patterns.
- [ ] There are no unrelated formatting rewrites.
- [ ] There are no generated leftovers.
- [ ] There are no obvious hardcoded local assumptions.
- [ ] Errors are handled clearly.
- [ ] Edge cases were considered.
- [ ] Tests, lint, build, or manual checks were run when practical.
- [ ] Results are reported honestly.
- [ ] Untested areas are clearly stated.
- [ ] Security-sensitive areas are flagged for HI review.

---

## Reviewer Mindset

Review code as if it came from a fast junior developer who may be confident but wrong.

Look for:

- plausible-looking incorrectness
- missing edge cases
- hidden assumptions
- overbroad changes
- needless abstractions
- inconsistent style
- unsafe shortcuts
- weak tests
- stale docs
- unverified claims

A code smell is not automatically a bug. It is a signal to inspect further.

---

## Scope Review

### Good Signs

- The diff is small.
- The files changed are directly related to the task.
- Refactors are separated from behavior changes.
- Public behavior changes are documented.
- The agent can explain why each file changed.

### Warning Signs

- Many files changed for a small request.
- Formatting changes are mixed with logic changes.
- The implementation rewrites working code without need.
- The task quietly became larger.
- The agent made extra improvements that were not requested.
- The diff is hard to review in one sitting.

### Reviewer Questions

- Does this solve the requested problem?
- Are unrelated files touched?
- Could this be split into smaller changes?
- Is this a feature, bug fix, refactor, or cleanup?
- Are those categories mixed together?
- Did the agent ask before expanding scope?

### Required Action

If scope is too broad, ask for a smaller patch or a staged plan.

---

## Comprehension Review

### Good Signs

- The agent names the affected code path.
- The implementation matches nearby patterns.
- The agent explains tradeoffs.
- Assumptions are stated.
- The code is readable without heroic effort.

### Warning Signs

- The agent cannot explain why the code works.
- The code looks pasted from another project.
- The implementation ignores existing helpers.
- The code duplicates existing behavior.
- The code uses unfamiliar patterns without rationale.
- The agent claims confidence without evidence.

### Reviewer Questions

- What existing code path does this affect?
- What assumptions does the change make?
- What happens if those assumptions are wrong?
- What existing project pattern does this follow?
- What alternatives were considered?
- Why is this the smallest safe solution?

### Required Action

If the agent cannot explain the change, do not trust the change yet.

---

## Maintainability Review

### Good Signs

- Names are clear.
- Functions have focused responsibilities.
- Components remain small enough to understand.
- Control flow is easy to trace.
- Constants are named.
- Comments explain why, not obvious what.
- The change reduces complexity or keeps it stable.

### Warning Signs

- Spaghetti code
- God objects
- Deep nesting
- Long functions
- Duplicated logic
- Magic numbers
- Magic strings
- Excessive comments explaining confusing code
- Abstractions with only one caller
- Generic helper layers with unclear purpose

### Reviewer Questions

- Is this easier to understand than before?
- Can a future maintainer safely change it?
- Is responsibility split cleanly?
- Is the public surface area minimal?
- Are names specific enough?
- Does this add complexity that pays rent today?

### Required Action

If maintainability gets worse, ask for simplification before approval.

---

## Integration Review

### Good Signs

- The change fits existing architecture.
- Existing config patterns are reused.
- Existing logging patterns are reused.
- Existing error-handling patterns are reused.
- Existing tests are extended instead of bypassed.
- Edge cases match real project behavior.

### Warning Signs

- New config format without need.
- New dependency for a small task.
- New logging style.
- New error style.
- New validation style.
- Parallel implementation of existing behavior.
- Integration code with no clear owner.
- Temporary code in a permanent path.

### Reviewer Questions

- Does this integrate with the rest of the system?
- Does this duplicate an existing helper?
- Does this create a stovepipe subsystem?
- Does this bypass established project conventions?
- Will future agents know where to extend this?

### Required Action

If the change creates an isolated subsystem, ask whether integration should happen now or the feature should stay experimental.

---

## Security Review

Run this section for any code touching:

- authentication
- authorization
- permissions
- cryptography
- password handling
- session management
- cookies
- tokens
- input validation
- output encoding
- file paths
- file upload
- file download
- shell commands
- subprocess calls
- SQL or query construction
- deserialization
- network requests
- CORS
- PII
- logs
- secrets
- dependency updates
- sandboxing

### Security Checklist

- [ ] Inputs are validated on the trusted side.
- [ ] Output is encoded or escaped where needed.
- [ ] Auth checks use existing project mechanisms.
- [ ] Authorization is enforced server-side or in the trusted layer.
- [ ] File paths are normalized and constrained.
- [ ] Shell commands avoid string interpolation.
- [ ] SQL or query construction uses safe parameterization.
- [ ] Secrets are not committed or printed.
- [ ] Sensitive values are not logged.
- [ ] Error messages do not expose internals to users.
- [ ] Cryptography is not invented from scratch.
- [ ] TLS/certificate verification is not disabled.
- [ ] CORS is not opened broadly without justification.
- [ ] Deserialization is safe for untrusted data.
- [ ] New dependencies are necessary and reputable.
- [ ] Security-sensitive changes are flagged for HI review.

### Warning Signs

- `shell=True`
- raw SQL strings with user input
- `except Exception: pass`
- disabled certificate checks
- hardcoded tokens or passwords
- custom password hashing
- client-side-only authorization
- broad CORS
- path joins using untrusted input
- debug stack traces exposed to users
- logs containing secrets or PII

### Required Action

Security-sensitive changes require elevated review.

If the agent is unsure, it must stop and flag the issue.

---

## Error Handling Review

### Good Signs

- Errors are specific enough for operators.
- User-facing errors are safe and understandable.
- Internal logs preserve useful context.
- Exceptions are not swallowed.
- Cleanup happens on failure.
- Retry behavior is bounded.

### Warning Signs

- Silent failure
- Catch-all exceptions
- Vague messages like “failed”
- Raw stack traces shown to users
- Sensitive data in logs
- Infinite retries
- Errors converted into misleading success states

### Reviewer Questions

- What happens when this fails?
- Will the user know what to do?
- Will the operator know what happened?
- Does the code leak sensitive details?
- Does failure leave corrupted state?

### Required Action

If errors are hidden or misleading, require a fix.

---

## Testing Review

### Good Signs

- Tests match expected behavior.
- Tests cover edge cases.
- Tests fail before implementation when possible.
- Existing tests are updated thoughtfully.
- Manual verification is documented when automated tests are not available.
- The agent reports exact commands and results.

### Warning Signs

- Tests only prove mocks work.
- Tests mirror implementation details.
- Tests avoid the risky path.
- No negative cases.
- No empty-state cases.
- No malformed input cases.
- Claims of success without command output.
- “Should work” instead of evidence.

### Reviewer Questions

- What behavior is tested?
- What behavior is not tested?
- Would this test fail if the implementation were broken?
- Are important edge cases covered?
- Was the full suite run or only targeted tests?
- Is manual verification enough for this change?

### Required Action

If testing is weak, either request better tests or require the agent to state the risk clearly.

---

## Documentation Review

### Good Signs

- User-facing behavior changes are documented.
- Config changes include examples.
- New commands include usage.
- New risks or limitations are stated.
- README, changelog, or dev notes are updated when needed.
- Documentation uses the project’s established voice.

### Warning Signs

- Docs describe old behavior.
- New config exists only in code.
- Examples do not match implementation.
- Risky limitations are omitted.
- Internal notes leak secrets or private context.
- Documentation is overly verbose or marketing-flavored.

### Reviewer Questions

- Would a user know how to use this?
- Would a developer know how to maintain this?
- Did behavior change without docs changing?
- Are limitations honest?
- Are examples accurate?

### Required Action

If docs are stale because of the change, update them before presenting work.

---

## Dependency Review

### Good Signs

- Existing dependencies are reused.
- New dependencies solve a real problem.
- The dependency is maintained.
- The dependency is appropriate for the project size.
- Lockfiles are updated when required.
- Supply-chain risk is considered.

### Warning Signs

- New dependency for trivial behavior.
- Abandoned package.
- Duplicate package with existing functionality.
- Heavy framework for a small task.
- Unpinned or suspicious dependency.
- Dependency added without explaining why.

### Reviewer Questions

- Can the standard library do this?
- Can an existing project dependency do this?
- Is this package trustworthy?
- Is this worth the maintenance cost?
- Does this affect packaging or deployment?

### Required Action

If a dependency is not clearly justified, remove it or ask HI.

---

## Performance Review

### Good Signs

- Work is proportional to input size.
- Expensive operations are bounded.
- Caching is justified and invalidation is considered.
- Large files or large result sets are handled deliberately.
- UI remains responsive.

### Warning Signs

- Unbounded loops
- Loading entire large files into memory
- Repeated network calls in loops
- Blocking UI operations
- Cache with no invalidation
- Hidden quadratic behavior
- Excessive logging in hot paths

### Reviewer Questions

- What happens with large input?
- What happens with no input?
- What happens with slow network or disk?
- Is this run once or often?
- Is caching necessary?
- How is cached data invalidated?

### Required Action

If performance risk is plausible, document it or test it.

---

## Non-Pattern Catalog

Use this catalog to name problems during review.

### Spaghetti Code

Code with tangled flow that is difficult to trace.

Check for:

- deep nesting
- unclear state changes
- logic split across unrelated places
- functions that require too much context to understand

Better approach:

- extract focused functions
- clarify state ownership
- reduce branching
- name intermediate concepts

### God Object

A component that does too many jobs.

Check for:

- one class handling UI, persistence, validation, networking, and business logic
- large files that keep growing
- unrelated methods grouped together

Better approach:

- split responsibilities
- define clear boundaries
- move helpers near their domain

### Shotgun Surgery

A small behavior change requiring many edits across many files.

Check for:

- repeated conditionals
- duplicated mappings
- scattered config
- parallel structures

Better approach:

- centralize the concept
- create one clear extension point
- avoid broad unrelated edits

### Cargo Cult Programming

Using code or patterns without understanding them.

Check for:

- copied patterns from another language or framework
- unexplained decorators
- unnecessary async
- abstractions with no local reason
- code that works only by accident

Better approach:

- explain the pattern
- remove what is unnecessary
- match the project’s real needs

### Golden Hammer

Overusing a favorite tool or pattern.

Check for:

- using the same pattern for every problem
- adding a framework where a function would work
- forcing inheritance, factories, or plugins everywhere

Better approach:

- choose the simplest tool that fits
- justify complexity with current requirements

### Fear-Driven Development

Avoiding necessary changes because the system feels fragile.

Check for:

- piling workarounds on top of broken code
- refusing to touch the real source of the bug
- adding duplicate paths to avoid existing code

Better approach:

- write a test or reproduction
- isolate the risky area
- make the smallest safe fix
- document the fragile part

### Stovepipe System

A feature built in isolation with weak integration.

Check for:

- separate config
- separate logging
- separate error style
- duplicate models
- no shared tests

Better approach:

- integrate with existing systems
- reuse project patterns
- document boundaries clearly

### Heisenbug

A bug that changes or disappears when debugging.

Check for:

- timing sensitivity
- race conditions
- global mutable state
- logging changing behavior
- debug-only success

Better approach:

- reduce nondeterminism
- add targeted instrumentation
- isolate state
- create a minimal reproduction

### Magic Numbers and Magic Strings

Unexplained literal values embedded in code.

Check for:

- raw timeouts
- raw status strings
- repeated numeric thresholds
- undocumented constants

Better approach:

- use named constants
- document why the value exists
- move user-tunable values to config

### Hardcoding

Embedding environment-specific values in source.

Check for:

- local paths
- usernames
- ports
- URLs
- model names
- credentials
- platform-specific assumptions

Better approach:

- use config
- use environment variables
- provide safe defaults
- document expected values

### Refuctoring

A change intended as cleanup that makes code worse.

Check for:

- behavior changed during cleanup
- clearer code replaced with clever code
- abstractions added without need
- tests not updated

Better approach:

- separate refactor from behavior change
- preserve behavior
- keep refactors small
- verify with tests

### Dead Code

Unused or unreachable code.

Check for:

- unused imports
- unused functions
- commented-out blocks
- unreachable branches
- stale feature flags

Better approach:

- delete it
- document why retained code must stay
- add tests if reachability is unclear

### Zebra Pattern

Multiple coding styles mixed in the same project.

Check for:

- inconsistent naming
- inconsistent formatting
- different error patterns
- different logging approaches
- mixed architectural styles

Better approach:

- follow nearby code
- avoid personal style rewrites
- make style changes separately if needed

### Factory Factory

Overuse of factory patterns or abstraction layers.

Check for:

- factories that only instantiate one thing
- registries with one entry
- generic abstractions with no second use case
- complex setup for simple behavior

Better approach:

- instantiate directly
- defer abstraction until there is a real second case
- keep extension points boring

### Over-Engineering

More structure than the problem needs.

Check for:

- speculative plugin systems
- excessive configuration
- premature generalization
- deep class hierarchies
- unnecessary dependency injection

Better approach:

- solve the current problem
- leave simple seams
- document future options without building them now

### Verbose Code

Code that says a simple thing in a noisy way.

Check for:

- needless wrappers
- long names that do not clarify
- repeated boilerplate
- comments restating code
- multi-step logic for simple operations

Better approach:

- simplify expressions
- remove wrappers
- use clear names
- prefer direct code

### Throwaway Creep

Prototype code becoming permanent by accident.

Check for:

- temporary files in core paths
- TODOs with no owner
- debug UI left enabled
- sample data treated as real data
- quick-hack code without boundaries

Better approach:

- isolate experiments
- mark temporary code clearly
- remove before merge
- create follow-up tasks if retained

### Fragile Glue

Integration code that connects systems but has weak ownership or error handling.

Check for:

- unclear data contracts
- silent conversion failures
- swallowed errors
- duplicated mapping logic
- no tests around boundaries

Better approach:

- define the contract
- validate inputs and outputs
- log failures clearly
- test the integration boundary

---

## Required Agent Self-Review

Before presenting work, answer:

```text
1. What did I change?
2. Why did I change it?
3. Which files did I touch?
4. Why was each file necessary?
5. What existing pattern did I follow?
6. What assumptions did I make?
7. What edge cases did I consider?
8. What tests or checks did I run?
9. What did I not test?
10. What should HI review most carefully?
```

If the answer to any item is weak, improve the work or flag the gap.

---

## Required Review Output

When reviewing code, use this structure:

```text
Summary:
- Briefly state whether the change is acceptable, needs revision, or needs HI decision.

High-Risk Issues:
- Security, data loss, broken behavior, destructive operations, or major architecture concerns.

Required Fixes:
- Issues that should be fixed before approval.

Suggested Improvements:
- Useful improvements that are not blockers.

Evidence:
- Tests, lint, build, manual checks, or files inspected.

Open Questions:
- Bounded questions for HI or the implementer.
```

Do not bury blockers in a long paragraph.

---

## Severity Labels

### Blocker

Must fix before merge or execution.

Examples:

- security vulnerability
- data loss risk
- broken core behavior
- destructive command without approval
- secrets exposed
- unreviewable diff

### Required

Should fix before approval.

Examples:

- missing test for changed behavior
- unclear error handling
- stale docs
- unnecessary dependency
- hardcoded project-specific value

### Suggested

Good improvement, not required.

Examples:

- clearer name
- small simplification
- extra edge-case test
- minor documentation improvement

### Nit

Tiny issue.

Examples:

- minor wording
- harmless formatting
- small style preference

Do not overuse nits.

---

## Security Escalation Template

Use this when a change touches elevated-risk areas:

```text
Security Review Required:

This change touches [auth/input validation/file handling/secrets/etc.].

Review focus:
- [specific risk 1]
- [specific risk 2]
- [specific risk 3]

Recommended checks:
- Run relevant tests.
- Run static analysis if available.
- Inspect error handling and logs.
- Confirm no secrets or sensitive data are exposed.
- Confirm the change uses existing project security patterns.

I do not recommend merging this without HI review.
```

---

## Final Pre-Submit Template

Use this before handing work back to HI:

```text
Completed:
- [short summary]

Files changed:
- [file]: [why]

Checks run:
- [command]: [result]
- [manual check]: [result]

Not tested:
- [honest gap]

Risks / limitations:
- [known concern]

Recommended next step:
- [one concrete next action]
```

---

## Hard Stop Conditions

Stop and ask HI before proceeding if:

- the task requires destructive commands
- the task changes authentication or authorization
- secrets are involved
- production data may be affected
- schema changes are required
- legal/compliance risk is plausible
- the change requires broad architecture decisions
- the implementation would require touching many unrelated files
- tests reveal behavior different from HI’s stated intent
- you are guessing instead of reasoning from evidence

---

## Quick Reference

A good change is:

- small
- direct
- understandable
- consistent
- tested where practical
- honest about risk
- boring in the best way

A bad change is:

- broad
- clever
- unexplained
- inconsistent
- untested
- full of assumptions
- difficult to review

When in doubt, reduce scope and show evidence.
