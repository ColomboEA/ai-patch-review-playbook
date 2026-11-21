# AI-Generated Patch Review Playbook

An agent’s explanation is untrusted review input. Review the diff, its effects, and the evidence needed to roll it back.

## Before running the patch

For third-party or agent-generated changes:

- inspect the diff before checkout/build execution;
- use an isolated workspace with no production credentials;
- disable or review install/lifecycle scripts and workflow changes first;
- deny network by default or allowlist only required test endpoints;
- snapshot the base commit and write the rollback command/owner;
- never let a scanner score or passing test auto-merge the patch.

## Triage by changed risk

| Change class | Automated evidence | Human gate | Stop / rollback trigger |
|---|---|---|---|
| Logic/API behavior | semantic diff, targeted tests, static/query analysis | invariants, compatibility, error paths, authorization | undocumented behavior or invariant violation |
| Auth/secret/data flow | CodeQL/Semgrep where applicable, Gitleaks, negative tests | trust boundary, minimum privilege, logging/redaction | secret exposure or widened access |
| Dependency/lockfile | lockfile diff, OSV/Trivy where supported | necessity, migration, install scripts, exact version/provenance | unexplained transitive or lifecycle change |
| Container/IaC | Trivy/policy checks, config diff | exposure, privilege, persistence, rollback | new public surface or privilege without approval |
| CI/release/workflow | syntax/policy checks only | token permissions, event triggers, artifact destination | unapproved publish/deploy/write permission |
| Generated/vendor/binary | hashes/SBOM/provenance if available | reproducibility and source | unexplained opaque artifact |

## Review sequence

1. **Describe intent from the ticket, not the agent narrative.** Write expected files, behavior, and forbidden effects.
2. **Inspect structure.** Difftastic may reduce syntax noise; it does not prove behavior.
3. **Check human invariants.** Use [`assets/review-card.md`](assets/review-card.md): authorization, data boundaries, failure/rollback, compatibility, and unnecessary churn.
4. **Run the smallest relevant analyzers.** Semgrep and CodeQL depend on language/query coverage; Gitleaks finds patterns, not every sensitive value.
5. **Review dependencies and artifacts.** OSV-Scanner/Trivy report known data; neither proves a release or new package is safe.
6. **Execute targeted tests in isolation.** Add negative tests for the changed trust boundary; do not run untrusted setup scripts in a credentialed host.
7. **Classify findings.** New regression, pre-existing baseline, false positive with rationale, or unresolved. Do not “clean the dashboard” by suppressing unexplained findings.
8. **Approve, hold, or revert.** Approval requires a human owner; preserve the base commit and rollback path.

## Tool roles—narrowly stated

- [Difftastic](https://github.com/Wilfred/difftastic): syntax-aware diff presentation.
- [Semgrep](https://github.com/semgrep/semgrep): rule-based pattern/dataflow findings for supported analyses.
- [CodeQL](https://github.com/github/codeql): query analysis over supported language databases.
- [Gitleaks](https://github.com/gitleaks/gitleaks): credential-pattern scanning in Git content.
- [Trivy](https://github.com/aquasecurity/trivy): vulnerability, misconfiguration, secret, license, and SBOM-related scan targets.
- [OSV-Scanner](https://github.com/google/osv-scanner): known-vulnerability matching for supported manifests/SBOMs.
- [reviewdog](https://github.com/reviewdog/reviewdog): presents supported analyzer output in review systems; **it is not a policy authority**.
- [OpenSSF Scorecard](https://github.com/ossf/scorecard): repository-practice signals for upstream context; **not evidence that a specific version or patch is safe**.

Tool pages support these roles only. The risk mapping, execution order, and human gates are **author recommendations in this playbook** and require project-specific validation.

## Baseline and secrets

A finding present on the base commit is not caused by the patch, but may still block release if the patch exposes it. For a suspected real secret: stop sharing logs, revoke/rotate through the proper owner, remove it from current/history locations as policy requires, and document exposure. Do not paste the value into an issue or review comment.

## Boundary with dependency automation

This playbook reviews **one patch of any type**. `dependency-agent-safety-gates` governs the full propose→resolve→build→approve→rollback lifecycle for dependency updates. Shared scanners are implementation options, not duplicated authorization logic.

## Maintenance

Update a tool role only from its specific upstream docs/version. Update the playbook when a new patch class or observed failure changes the human gate. Date/star changes alone do not justify an edit.

Source ledger: [`sources/sources.json`](sources/sources.json). Review artifact: [`assets/review-card.md`](assets/review-card.md).

## License

Original playbook and review card are available under CC BY 4.0. Linked tools retain their own licenses.
