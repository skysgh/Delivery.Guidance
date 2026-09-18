# Startup Conventions

Read this document when adding or changing application startup, module initialisation, service registration, configuration binding, registries, schema discovery, settings, seed initialisation, middleware composition or startup diagnostics.

For the accessible explanation of startup as the stage before a building opens, read [Reader Guidance](../../../IT/readme.md).

## Purpose

This document explains how a service becomes ready to receive work. It is for developers, testers, operations staff, security roles and anyone who needs to understand what must happen before the first request is safe to process.

## The short version

Startup is the building stage before the building opens. The host finds the parts it needs, checks that they fit, connects them, prepares storage and external services, and records what happened. If a required part is missing or unsafe, the service should not claim to be ready.

In technical language, this deliberate assembly is called the startup lifecycle. The term adds precision. The important idea is that configuration, settings, schema, storage, mapping, caching, security and application capabilities must become available in a known order.

Each role contributes a different view:

- developers make participants implementable and testable;
- testers check discovery, order, failure and repeat-start behaviour;
- operations checks readiness, diagnostics and recovery;
- security checks secrets, permissions and information exposure; and
- architecture keeps the overall sequence coherent.

## Startup is platform assembly

Startup is the deliberate assembly of the platform from objects that represent configuration, settings, schema, storage, caching, mapping, capabilities, policies, registries, initialisers and pipeline components. These concerns overlap because they all answer the same startup question: what does the host need to discover, validate, connect, register and make ready before it can safely serve requests?

Prefer an object with a clear contract, lifecycle, owner, order and diagnostic identity over spare arguments passed through unrelated methods or assembled only when a later caller happens to need them.

The usual distinction is:

- **Configuration** supplies typed values needed to construct or operate the application.
- **Settings** describe user- or scope-manageable choices, their metadata, validation and effective-value rules.
- **Storage schema** defines the durable shape and readiness of persisted data.
- **Caching** defines reusable data, key, expiry, invalidation, fallback and provider policy.
- **Mapping** defines how internal models become boundary models and how query projection remains safe.
- **Registries and discovery** collect the definitions and providers that make those capabilities available.

They should share lifecycle and diagnostic discipline without being collapsed into one vague configuration mechanism.

Create and validate these objects as early as their dependencies permit. Early object assembly makes the system's composition visible, allows conflicts and missing dependencies to be rejected before requests arrive, and gives diagnostics something meaningful to report. It does not mean every object must be instantiated before configuration or dependency injection is available; it means the required lifecycle phase should be explicit.

## Contract-based reflection

Reflection is a discovery mechanism, not a design model. Discover types because they satisfy a capability contract, lifecycle contract or explicit discovery attribute. Do not rely on a class name, filename or namespace as the meaning of the capability.

Service registration follows the same contract-led rule. A class or its service
contract inherits the applicable lifecycle marker, and startup reflection resolves
that marker into the dependency-injection lifetime. The marker is the registration
contract; a class name or folder is not.

Hard-wired registration is a valid bootstrap technique and may be the right choice for a genuinely tiny, closed or early-stage system. It is not the maturity target for a modular platform. As modules and participants grow, hard-wiring hides the composition graph in host code, makes omissions easy, encourages copy-and-paste registration, and prevents the platform from proving what was or was not assembled.

Contract-based reflection is preferred once the contract, discovery scope, ordering rules, activation policy and startup diagnostics are mature. Reflection is not accepted merely because it reduces lines of startup code. It is accepted because it makes module composition extensible while remaining inspectable, deterministic and testable. A team must not reject it as "magic" without addressing the contract, diagnostics and tests that make the mechanism understandable. If the mechanism cannot be explained, logged and tested, the mechanism is not mature enough yet; the answer is to improve that evidence, not to retreat to an ever-growing list of hard-wired calls.

A discovery contract should make the following answerable:

- what capability the object provides;
- which lifecycle phase may invoke it;
- how dependencies are obtained;
- whether it is stateful and what lifetime it requires;
- how execution order or dependencies are determined;
- how ambiguity and duplicates are handled; and
- how discovery, activation, failure and completion are reported.

Concrete repositories may call these contracts configuration-object, registry, schema-definition, service-configurer, initialiser, middleware, mapping or setting contracts. Those names are bindings, not the portable architecture. The portable concept is a contract-bearing startup participant.

Do not use reflection to discover arbitrary methods and infer their meaning from method names when a contract can express the capability. If a compatibility bridge must inspect a legacy shape, keep it scoped, visible, deterministic and temporary.

## Lifecycle phases

A host commonly needs phases with these responsibilities. The exact names may vary, but the dependency order must remain observable:

1. **Preflight and host configuration**: establish environment, dependency targets, secrets/configuration sources and safe startup diagnostics.
2. **Assembly and module discovery**: identify eligible assemblies, group them by logical module, apply exclusions, load them and establish deterministic order.
3. **Contract discovery**: collect startup participants into a module-owned composition record. Discover configuration objects, services, registries, schema definitions, contexts, maps, middleware, settings and initialisers without invoking work prematurely.
4. **Pre-build registration**: bind configuration objects, register services and registries, add framework services, and invoke only operations that need the service collection rather than a built provider.
5. **Container build and validation**: build the provider, validate required registrations and publish the discovered composition and diagnostics.
6. **Schema and persistence readiness**: apply or migrate schemas and make required persistence infrastructure available before hooks that query or write it.
7. **Post-build initialisation**: invoke objects that require resolved services, such as registry population, definitions loaded from persistence, cache preparation or module hooks.
8. **Ordered service configuration**: run configuration objects that require the built provider in declared dependency order. Reject ambiguous or duplicate ordering where it affects correctness.
9. **Runtime seeding and external preparation**: run idempotent initialisers that depend on databases, object storage, identity providers or other external infrastructure.
10. **Pipeline composition and finalisation**: compose middleware and endpoints in security-sensitive order, expose readiness only when required phases have completed, and emit a final startup record.

A phase may fail, degrade or be skipped only under an explicit policy. Startup must report the phase, participant, reason, impact and recovery path.

## Object categories

Use these conceptual categories when deciding what belongs in startup:

- **Configuration object**: a typed, owned set of settings bound from a source and validated before dependent services use it.
- **Definition object**: metadata describing a resource, setting, permission, schema element, capability or other thing that the system must know about.
- **Registry object**: a stateful, usually singleton index or broker that collects providers, handlers, descriptors or runtime entries and exposes governed lookup or invocation.
- **Cache policy/object**: a typed description of a cacheable capability, provider suitability, key policy, expiry/timeout behaviour and refresh or fallback operation. A timeout callback is part of the policy object, not a loose delegate floating through startup.
- **Schema definition**: a contract-bearing description of persistence shape or configuration that the persistence boundary can discover and apply. Keep provider-specific details in the persistence binding.
- **Initialiser**: a lifecycle operation that prepares a module or external state. It must declare whether it runs before or after provider build, whether it is idempotent and what it requires.
- **Service configurer**: an ordered operation that configures a service after the required provider or dependency exists.
- **Pipeline component**: a contract-bearing middleware, endpoint, handler or tool contribution with a declared placement and order.
- **Mapping participant**: a contract-bearing map or projection contribution that defines how an internal model is safely represented at a boundary and remains compatible with provider-side query translation where required.
- **Setting declaration**: a typed definition of a setting's key, data type, default, validation, classification, ownership and presentation metadata. A setting declaration is not the same thing as a user's setting value.
- **Startup diagnostic record**: structured phase, participant, timing, status, metrics, exception and child-step information. Do not make operators reconstruct startup from unstructured console text.

These categories may overlap in a concrete implementation, but the lifecycle responsibility must remain unambiguous.

## Lifetime north star

Prefer singleton lifetime for stateless, thread-safe services, immutable policies,
startup-hydrated definitions and registries when their dependencies are also safe for
singleton use. This reduces allocation, memory churn and garbage-collection pressure,
and gives startup one observable object to validate and diagnose.

This is a north star, not a command to make everything singleton. Controllers,
application services and request-sensitive context objects are normally scoped to a
request or operation. Repositories are commonly scoped because they share a
DbContext or unit of work. Any object that carries mutable request state, scoped
identity, transaction state or non-thread-safe dependencies must not be promoted to
singleton merely to reduce allocations. Correct lifetime and ownership outrank the
singleton preference.

## What startup must make visible

Before readiness, startup should make it possible to answer:

- which configuration sources were accepted and which typed objects were bound;
- which settings definitions and value providers were discovered;
- which storage schema participants were collected and whether persistence is ready;
- which cache providers and cache policies were registered;
- which mappings and query projections are available;
- which module and capability participants were discovered;
- what order was selected and why; and
- which failures, degradations or exclusions changed the resulting system.

This is why startup logging is part of the architecture rather than a debugging afterthought. A participant that is discovered through reflection but absent from the startup record is not operationally understandable. A participant that is hard-wired but absent from the same record is not safer; it is merely harder to inspect.

## Configuration and settings

Bind configuration into typed objects before dependent services are registered or activated. Validate required values, defaults, sources, classification and cross-property rules at the earliest phase that has enough information.

Keep these concepts separate:

- host configuration used to assemble the application;
- module configuration used by a service or infrastructure adapter;
- declared settings that describe available user or workspace choices; and
- persisted setting values owned by a user, workspace or other actor.

Do not pass raw configuration keys, optional values and fallback delegates through many constructors. Give the owning object a contract and let startup bind, validate and register it once.

## Registries and timeout operations

A registry is an owned runtime object, not a static bag of global functions. It should define its state, lifetime, registration rules, lookup semantics, duplicate policy, thread-safety, maximum size and diagnostic identity.

For cache or registry entries that need timeout, expiry or refresh behaviour, keep the operation with the object that owns the policy. The object should express the key, value or capability, expiry semantics, cancellation, retry limits, fallback behaviour, observability and failure policy. Startup registers the object or provider through its contract; later code resolves the registry rather than receiving unrelated delegates as spare arguments.

Timeout callbacks must be bounded, cancellation-aware and safe to retry. They must not silently turn a cache miss into an unbounded request fan-out or bypass authorisation and classification policy.

## Cache provider choice

Caching is optional infrastructure. Design a cacheable capability and its policy when latency, load, availability, provider cost or a resilience boundary justifies caching; do not add a cache merely because the platform can host one.

The platform should support at least two provider families:

- **In-memory cache**: local to one process. It is often appropriate for small, inexpensive, rapidly rebuildable values where each instance may hold its own copy and a restart may discard the value.
- **Shared cache**: available to multiple processes or instances. It is appropriate when consumers need a common value, coordinated refresh, cross-instance reuse or a cache lifetime that should survive an individual process restart. It brings network, availability, consistency, security and operational costs.

The cache object should express which provider characteristics it requires, while the cache-object service and registry select the applicable provider. Downstream consumers should request the cacheable capability, not choose a library, know whether the value is local or shared, or carry provider-specific fallback logic. Provider choice is a platform policy decision recorded in startup diagnostics and constrained by the cache object's classification, consistency and failure requirements.

## Schema and model discovery

Discover schema participants by a schema capability contract and scope discovery to the owning module's assemblies. Keep discovery deterministic, report the count and identity of discovered types, reject ambiguous ownership, and ensure design-time and runtime inputs produce the same model where generated schema depends on them.

Schema discovery should collect descriptions before applying provider-specific model operations. The persistence boundary owns the actual model builder, migrations and provider details. Startup owns readiness, ordering, diagnostics and failure handling.

## Ordering and dependencies

Prefer explicit dependency metadata or stable order values over incidental reflection order. A participant that depends on another participant must express that dependency or belong to a later phase.

For each ordered category:

- define the default order range or dependency rule;
- sort with a stable secondary key;
- detect duplicate or contradictory order where it matters;
- log the selected order; and
- make repeated startup safe where the operation may run again.

Do not solve ordering by adding more static calls to the host when a contract can express the lifecycle relationship.

## Failure and observability

Every startup participant should have a meaningful display identity and produce structured success, warning, degraded or failure information. Include phase, module, participant, duration, dependency and impact where appropriate.

Fail before serving traffic when a missing or invalid object makes the service unsafe or functionally false. Degrade only when the degraded capability and operator response are explicit. Readiness must reflect the actual completion of required phases, not merely that a process has opened a port.

## Implementation checklist

When adding a startup participant:

1. State the capability and owning module.
2. Choose the narrowest existing contract that expresses it; create a new contract only when the semantic distinction is real.
3. Declare lifetime, required phase, dependencies, order, idempotency and failure policy.
4. Add it to contract-based discovery or explicit registration when discovery is not safe.
5. Validate configuration and reject ambiguity before dependent work runs.
6. Keep provider, persistence and vendor details behind their owning boundary.
7. Add structured startup diagnostics and a focused discovery/lifecycle test.
8. Verify cold start, repeated start, missing dependency, timeout, duplicate registration and partial failure behaviour.

The goal is not maximum reflection or maximum framework machinery. The goal is visible, contract-led composition that is deterministic, testable and ready at the correct point in the lifecycle.
