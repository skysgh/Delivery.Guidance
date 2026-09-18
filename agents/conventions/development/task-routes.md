# Task Routes

Use this page as the short first route before opening the detailed conventions.
Read the baseline column for every engineering task, then add only the rows that
match the work.

## Baseline

1. [Development Principles](../foundations/principles.md)
2. [Development Constraints](../foundations/constraints.md)
3. [Holistic Development Architecture](./component-architecture.md)
4. [Agent Workflow](../documentation/agent-workflow.md)
5. [Done](./done.md)

## Choose The Route

| Work in front of you | Add these conventions | Main question to answer |
| --- | --- | --- |
| New service capability or endpoint | [Developer Architecture Route](./guidance-for-developers.md), [Vertical Slices](../capabilities/slices.md), [Code](./code.md), [Testing](./testing.md) | Which LDM, domain, contract and complete slice own this capability? |
| Persistent read or query change | [IQueryable](../foundations/iqueryable.md), [Vertical Slices](../capabilities/slices.md) | Where are visibility, projection, query cost and materialisation governed? |
| Entity, schema, migration or seed change | [Projects](./projects.md), [Startup and discovery](../foundations/startup.md), the repository's persistence binding | Which contracts and extension methods define the schema before primitives are used? |
| Configuration, settings, permissions or startup discovery | [Startup and discovery](../foundations/startup.md), [Registries](./registries.md), [Data protection](../foundations/data-protection.md) | Is this host configuration, runtime settings, a permission definition or a registry participant? |
| Cross-capability journey | [Horizontal Flows](../capabilities/flows.md), [Vertical Slices](../capabilities/slices.md) | Which slices retain ownership while the flow coordinates them? |
| Project, namespace or folder restructuring | [Projects](./projects.md), [LDMs](./ldms.md), [Holistic Development Architecture](./component-architecture.md) | Does the physical location expose the same ownership and dependency direction as the code? |
| Cleanup or review | [Code](./code.md), [Agent Workflow](../documentation/agent-workflow.md), [Done](./done.md) | Is this drift, a deliberate exception or a design decision that needs evidence? |
| Security, personal or classified data | [Data protection](../foundations/data-protection.md), [Design - Security](./design-security.md) | Who may see, change, retain, disclose or recover this information? |
| Dependency, operation or delivery change | [Dependencies](./dependencies.md), [Operations](./operations.md), [Performance](./performance.md) | What new lifecycle, capacity, support and recovery obligation is introduced? |

## Before Editing

Name the concrete anchor, the owning boundary, the contract relied on, the expected
vertical path and one focused check that could disprove the current hypothesis. If
the location or ownership is unclear, resolve that question before creating a type.

## Before Calling It Done

Check diagnostics, focused behaviour, the affected project, the full repository
build where available, and the final diff. Report what was changed, what was
validated, what could not be validated and what remains deliberately unresolved.

[Up: Development Conventions](./readme.md)