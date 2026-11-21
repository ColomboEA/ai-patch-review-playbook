# Patch review card

Patch / base commit: `<fill>`  
Reviewer / rollback owner: `<fill>`

## Intent and boundaries

- Expected behavior/files:
- Forbidden effects:
- Data/auth/network boundary:
- Rollback command or procedure:

## Risk-class checks

| Risk class | Evidence run | New / baseline / false positive / unresolved | Human invariant | Decision |
|---|---|---|---|---|
| Logic/API behavior |  |  |  |  |
| Auth/secrets/data flow |  |  |  |  |
| Dependencies/lockfiles |  |  |  |  |
| Container/IaC |  |  |  |  |
| CI/release/workflow |  |  |  |  |
| Generated/vendor/binary |  |  |  |  |

## Isolation

- [ ] Diff inspected before executing untrusted code.
- [ ] Disposable workspace; no production credentials.
- [ ] Install/lifecycle scripts and workflow changes reviewed.
- [ ] Network denied or explicitly allowlisted.
- [ ] Negative tests cover changed trust boundaries.

## Decision

- [ ] Approve by named human owner
- [ ] Hold with unresolved items and retest owner
- [ ] Revert/rollback verified

A scanner score, clean dashboard, or agent explanation is not approval.
