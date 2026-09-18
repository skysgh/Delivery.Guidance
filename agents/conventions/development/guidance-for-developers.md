# Developer Architecture Route

This is the agent-facing route for developer architecture decisions. The accessible explanation is [Guidance for Developers](../../../IT/foundations/guidance-for-developers.md). Read it when the task needs the reasoning behind these rules; use this document to apply the rules consistently.

Use the canonical [Holistic Development Architecture](./component-architecture.md) for the repeatable development shape from service and LDM ownership through domain, project, namespace, model contracts, schemas, mappings, slices and flows. Repository bindings supply concrete names.

The precision in that route is intentional. It is mature, reusable formwork for
reducing uncertainty and risk, not a prohibition on asking questions or exercising
judgment. Qualify a pattern when the facts genuinely differ, and record the reason
instead of replacing a known structure with an unexplained local invention.

## Required design route

For a new capability, module, service, endpoint, persistence model or cross-cutting integration, use these as dependencies rather than a rigid one-person-at-a-time sequence:

1. Identify the conceptual meaning and outcome that must be preserved.
2. Model the relevant business or technical domain ontologically: things, identities, relationships, states, decisions, rules, policies and evidence.
3. Assign the meaning, data and responsibility to the owning Logical Deployment Module (LDM) or boundary.
4. Define the contract, including security, lifecycle, data ownership and failure behaviour.
5. Keep dependency direction inward: interfaces and infrastructure depend on application and domain contracts; the domain does not depend on transport, persistence, vendors or frameworks.
6. Build one vertical slice using the contract-first construction and runtime paths in [Holistic Development Architecture](./component-architecture.md).
7. Model horizontal flows separately when several slices must be coordinated.
8. Choose the execution phase and mode: deployment, startup, synchronous operation or durable asynchronous work.

Steps 1 and 2 need business or technical evidence. Steps 3 and 4 usually develop together as the responsible boundary and its contract become clearer. Step 5 is a constraint throughout the work. Begin testing and recording deliberate deferrals as soon as the contract is concrete; do not wait until every later implementation detail is finished.

Do not derive the domain model directly from a screen, endpoint, current organisation chart, vendor API, database record or framework type. Use explicit mappings when models have different consumers or responsibilities.

## Non-negotiable boundaries

- A domain is an ontological model of a coherent business or technical problem space.
- An LDM is a deployment and ownership boundary with a coherent purpose, contracts, data and operational responsibilities.
- A vertical slice owns one complete capability and its test boundary.
- A horizontal flow coordinates slices but does not steal their rules, data or state ownership.
- UI and API interfaces are consumers of capabilities, not owners of domain meaning.
- Contracts must make participants isolatable, testable, replaceable and observable.
- Configuration, settings, startup, access control, mapping, schema, caching and operations are first-class technical domains, not unowned plumbing.
- Do not introduce a service boundary to conceal unclear ownership. Make the contract and module boundary clear first.
- Do not expose persistence records or domain entities directly at an external boundary.
- Do not make work asynchronous merely to avoid defining a synchronous contract.

## Lifecycle and execution

Startup is the composition root. It must discover or register participants deliberately, validate configuration and dependencies, prepare mappings and storage, establish readiness and report diagnostics. A participant that is discovered but never invoked is not an implemented lifecycle.

Use synchronous execution when the caller needs a bounded result immediately. Use a durable queue when work is long-running, independently retryable, temporarily unavailable or must survive caller disconnection. Queued work requires durable identity, correlation, status, retry and backoff, idempotency, cancellation or expiry, poison-message handling, reconciliation and operator visibility.

## Minimum evidence before ready

Before declaring a capability ready, verify:

- the owning domain and LDM are named;
- contracts and mappings are explicit;
- dependency direction is valid;
- access, data, audit and lifecycle rules are enforced at the correct boundaries;
- startup participation and readiness are observable;
- persistence or external effects have a failure and recovery policy;
- the vertical slice is independently testable;
- every coordinating flow has explicit hand-offs, retries, permissions and recovery; and
- deliberate deferrals are recorded rather than left as inferred future work.

When a local requirement conflicts with a rule, record a Deliberated Divergence with the alternatives, reason, consequences, scope, steward and review condition. Do not silently weaken the boundary.

## Detailed conventions

- [Development Principles](../foundations/principles.md)
- [Development Constraints](../foundations/constraints.md)
- [Logical Deployment Modules](./ldms.md)
- [Vertical Slices](../capabilities/slices.md)
- [Horizontal Flows](../capabilities/flows.md)
- [Startup and discovery](../foundations/startup.md)
- [Operations](./operations.md)
- [Repository bindings](../documentation/bindings.md)
- [Agent Workflow](../documentation/agent-workflow.md)
