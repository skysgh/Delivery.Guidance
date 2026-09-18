# Done

Apply this checklist to every change, selecting only the validation steps relevant to the affected files and behaviour.

For the accessible explanation of shared completion evidence, read [What the Guidance Gives Each Stakeholder](../../../IT/foundations/what-this-guidance-gives.md), [Deliverable Systems](../../../IT/shared/reference/catalogues/deliverable-systems.md), [Deliverables](../../../IT/shared/reference/catalogues/deliverables.md) and [Deliverables Checklist](../../../IT/shared/reference/checklists/deliverables.md).

## Purpose

This checklist helps the team decide whether a change is ready to be relied upon. It is shared evidence for developers, testers, operations, security and delivery roles, not a final ceremony owned by one role.

## The short version

Done means more than ÔÇ£the code runs.ÔÇØ It means the intended behaviour is implemented through the right boundaries, the important failures have been considered, the change is tested at the right level and any deliberate deferral is visible.

Validate work in increasing scope before declaring it complete.

1. Verify that all applicable conventions have been followed.
2. Check diagnostics for every changed file.
3. Run focused tests for the changed behaviour.
4. Build the affected project or logical module.
5. Run the repositoryÔÇÖs complete build task, even when focused builds have passed.
6. Run all applicable static and dynamic tests.
7. Where persistence has changed, verify migrations and pending model changes in both design-time and running-host contexts.
8. Inspect the final diff for unrelated changes, accidental contract drift, unexplained values, duplication and unintended generated artefacts.
9. Report what changed, what was validated, what could not be validated and any remaining limitation.

Treat warnings as errors. Correct relevant warnings rather than broadly suppressing them. Use the narrowest justified source-level suppression only when the warning is understood and correction would make the implementation less accurate or maintainable.

Validation is intentionally fail-fast. When the build or analyser reports a warning
as an error, stop at that failure, fix or narrowly justify it, and rerun the same
focused check before continuing. Do not pile new warnings onto an existing backlog;
warning debt is not an acceptable baseline for new work.

Work is complete only when the reachable behaviour is implemented through the appropriate architectural boundaries; applicable security, auditing, data and mapping conventions have been followed; no stub or knowingly incomplete implementation has been presented as complete; and all required validation has passed.

For a capability or cross-cutting change, completion evidence MUST show the intended contract, stewarding boundary, participating objects and services, relevant startup or registry participation, access and security decisions, mapping and persistence policy, tests, and any deliberate deferrals. A general statement that the team "already does this" is not evidence for the current capability.

If required validation fails or cannot be performed, report the work as incomplete or blocked, explain why and recommend corrective action.
