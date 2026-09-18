# Development Principles

Engineering work becomes easier to review when the team can explain not only what a convention asks for, but what enduring concern it protects. This document keeps that compact rationale for agents and maintainers. The more specific conventions selected for a task should make sense as consequences of these priorities rather than as isolated rules.

These principles are portable engineering priorities. They are not product requirements, project-management preferences or a list of framework names. Their purpose is to connect everyday technical choices to the service's long life, its boundaries and contracts, the people who depend on it and the evidence needed to trust it.

The [Reader Guidance](../../../IT/readme.md), [Deliverable Systems](../../../IT/shared/reference/catalogues/deliverable-systems.md) and [Deliverables](../../../IT/shared/reference/catalogues/deliverables.md) routes provide the accessible explanations and examples. This page remains deliberately compact because it is the agent-facing rationale behind those wider explanations.

## How to read this

These principles describe the ground beneath the building. They explain why boundaries, contracts, security, lifecycle, mapping and operational evidence matter before a team starts adding visible features.

The ideas can be understood before their technical names are familiar. Each principle starts with a practical concern and then introduces more precise engineering language where that helps different contributors discuss the same design. Business analysts bring the meaning and consequences of decisions. Developers test whether the priorities can become working contracts. Testers look for evidence that the boundaries behave as described. Operations brings lifecycle, readiness and recovery concerns. Architecture keeps the structure coherent across capabilities.

## Audience and quality standard

These principles are for meaningful applications that affect people, organisational work, records, access, decisions, obligations or reputation. They are not a default recipe for disposable trinkets or proof-of-concept demonstrations. An MVP may define a smaller first scope, but it does not lower the quality standard: the delivered scope must still be valuable, secure, usable, maintainable, operable and recoverable. Funding may not continue, so do not defer a known foundation on the assumption that a later phase will make an unsafe or unmaintainable choice correct. Defer capabilities when necessary; do not defer the responsibility to make the delivered system dependable.

## 1. Optimise the whole service life

A system spends far longer in operation, support and maintenance than in initial delivery. Optimise for the total cost of ownership, not only for the first successful build. A shortcut is not cheap if it creates a permanent operational dependency, repeated support work, or a difficult migration after the original developers have moved on.

This does not justify speculative implementation. It requires distinguishing design completeness from build completeness and matching construction discipline to consequence. A short-lived script, a contained internal tool and an externally relied-upon service do not need identical machinery. The reusable engineering structure is often knowable from decades of enterprise practice, even when the order and budget for building every instance are not. Do not invoke YAGNI to avoid defining a known boundary, contract or lifecycle when the reach, life or consequences of the service require it.

**Source:** The ISO/IEC 25010 quality model treats maintainability, performance efficiency, reliability, security and portability as product quality characteristics, not as post-delivery decoration. See the [ISO/IEC 25010 overview](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010).

## 2. Preserve evolvability at boundaries

Design boundaries so that future consumers can ask for more than the first user interface required, without forcing a new endpoint shape or a rewrite of the persistence path. This is the reason queryability is valuable.

Where a read is queryable, a repository should expose a governed `IQueryable` surface, the application service should compose the use-case query, and the controller should invoke the application service. The repository owns the source, visibility policy and provider boundary. Queryability does not mean exposing `DbContext` or permitting arbitrary persistence operations.

This supports reuse that was not fully imaginable during development: new screens, exports, integrations, support tools, reporting, accessibility needs, filtering, projection, ordering and paging can often be added by composing an existing governed read path. It also reduces the pressure to preserve brittle fixed endpoint shapes after the original developers are gone.

**Sources:** [Microsoft, `IQueryable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.linq.iqueryable-1) describes a queryable data source whose query is represented as an expression tree; [OData](https://www.oasis-open.org/standards/odata/) defines a standard way to query and represent resources; [Microsoft, DDD and layered architecture](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice) discusses keeping domain and infrastructure concerns behind application boundaries.

## 3. Model every domain ontologically before shaping it for consumers

Domain thinking is not reserved for customer-facing business concepts. Every coherent problem space has a domain, including technical and platform domains. Within an LDM, the application and domain implementation should physically express the **ontological model of the business domain**. A platform capability should physically express the equivalent ontological model of its technical domain: what configuration, identity, persistence, messaging, startup, diagnostics, caching, deployment or recovery concepts exist, how they relate, which states and failures matter and which contracts other parts may rely on. Interface DTOs and view models may be physically shaped closer to consumer language, while persistence models physically carry the meaning into storage and add the keys, constraints, indexes and other requirements that storage needs.

Each domain model is logical because it is independent of a particular transport, vendor or storage implementation. Domain code is a physical representation of that logical model, and it is conceptual within its own problem space because it is how the system knows what that domain means. A technical domain is not merely a wrapper around a library. Its model should express the capability, policy, lifecycle and failure semantics that make the technical concern useful to the rest of the system.

Keep that logical capability, relationships, state and queryable meaning intact for as long as possible. Delay consumer-specific shaping until the presentation or integration boundary where the actual need is known.

This does **not** mean exposing domain entities, persistence records or unrestricted queries. It means separating an ontological domain model from a consumer's current screen, process, vendor API, framework type, report shape or endpoint convenience model. Application and technical contracts should translate between those views and preserve useful semantics through governed projections or adapters. The final interface, view, export, integration adapter or infrastructure provider may then shape the domain meaning for its consumer.

Look for recurring structure before inventing a new domain concept. When several capabilities share an invariant, prefer a stable ontological abstraction and explicit mapping at the edge. When only one consumer needs a shape, keep it at that consumer boundary rather than allowing it to redefine the underlying capability.

This is not a demand to follow DDD or ANSI/SPARC terminology as doctrine. Those traditions provide useful tools and warnings. The objective is long-term system value: abstract from real business or technical needs and evidence so that today's application view, vendor choice or framework arrangement does not become tomorrow's system constraint. Do not invent an elegant domain model without evidence either. The team must make the difficult judgement about what is durable, what is local and what must remain explicitly different.

This improves evolvability because later consumers can ask different questions without requiring a new persistence path or a workaround around the first screen. It reduces novel solutions because established resource, relationship, query and lifecycle patterns remain reusable. It also respects long-lived patterns such as separation of concerns, information hiding, abstraction barriers and representation independence instead of rediscovering them as bespoke presentation models.

Presentation shaping may be forced earlier by security, data classification, performance, bandwidth, accessibility, protocol compatibility or cognitive load, but it should be reserved, limited and avoided where the requirement can be met at the presentation or integration boundary. Use early shaping only when no less-constraining option is adequate. Record the reason, the alternatives rejected and the information intentionally removed or future composition constrained.

**Sources:** [Microsoft, DDD-oriented microservice architecture](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice) discusses separation between domain, application and infrastructure concerns; [Martin Fowler, Presentation Domain Data Layering](https://martinfowler.com/eaaDev/PresentationDomainDataLayering.html) describes separating presentation, domain and data responsibilities; [ISO/IEC 25010 overview](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010), especially maintainability, flexibility and functional suitability.

## 4. Prepare for scale; spend as little as needed

Design so horizontal scaling remains possible when demand requires it, but do not buy that scale in advance with unnecessary infrastructure. The default target is the least resource-intensive deployment that satisfies the real workload: a single device or process may be preferable, and caching is not automatically an improvement. Where caching is justified, distinguish local in-memory caching from shared caching and select the provider behind the cacheable capability rather than making consumers depend on the provider.

Use shared cache, externalised state, queues or additional replicas when a measured requirement or a resilience boundary justifies them. Treat every always-on service as an operational liability: it needs monitoring, upgrades, security maintenance, capacity planning and failure handling.

The governing trade-off is: **prepare for the worst credible demand; operate at the smallest adequate footprint.** Measure before adding cache or distributed infrastructure, and measure again after adding it.

**Sources:** [AWS Well-Architected, Cost Optimization](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html) and [Azure Well-Architected, Cost Optimization](https://learn.microsoft.com/en-us/azure/well-architected/cost-optimization/) both treat resource efficiency and cost as architectural concerns. [NIST SP 800-160 Volume 1](https://csrc.nist.gov/pubs/sp/800/160/v1/r1/final) frames systems engineering as a lifecycle discipline rather than a one-time construction activity.

## 5. Contain dependencies at deliberate boundaries

Do not wrap a dependency merely to make the code look abstract. Wrap it when the boundary protects the rest of the system from vendor APIs, version churn, persistence semantics, transport concerns, or an operational policy that should be expressed in application language.

The wrapper must earn its existence by reducing coupling, enforcing policy, or making substitution and testing meaningful. It should not be a pass-through layer that adds names without adding ownership.

Persistence frameworks are difficult to contain completely, but containing them in one infrastructure boundary is still valuable when done consistently. The cost is paid once; the resulting boundary can be reused across many domains and maintained by the organisation rather than rediscovered in every feature.

The same principle applies to web frameworks, configuration, logging, caching, storage and external identity systems. Higher layers should depend on app-specific contracts where those contracts protect meaningful decisions.

**Sources:** [Martin Fowler, Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html) explains dependency inversion and separation of construction from use; [Microsoft, Dependency injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection) documents the platform's service composition model; [Microsoft, EF Core overview](https://learn.microsoft.com/en-us/ef/core/) documents EF as a persistence technology whose concerns belong at an infrastructure boundary.

## 6. Keep boundary technologies replaceable

Web transport belongs at the web boundary. Persistence belongs at the persistence boundary. Configuration belongs in typed purpose-specific options. Logging belongs behind the application's diagnostic contract when the application needs stable semantics, correlation, audit or structured events.

Each adopting repository should name its concrete web boundary in its binding document. The portable rule is that web concerns must not leak into domain or application contracts.

**Sources:** [Microsoft, ASP.NET Core overview](https://learn.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core) describes ASP.NET Core as a framework for HTTP applications; [Microsoft, Options pattern](https://learn.microsoft.com/en-us/dotnet/core/extensions/options) describes strongly typed access to related configuration; [Microsoft, logging in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging) documents structured logging and provider-based logging; [OpenTelemetry Logs](https://opentelemetry.io/docs/specs/otel/logs/) defines a vendor-neutral model for logs.

## 7. Prefer explicit policy over ambient access

Ambient access is convenient during development but difficult to reason about in operations. Prefer an injected, purpose-specific contract over repeatedly reaching for general-purpose `IConfiguration`, a framework logger, a service locator, static state or a database context.

This makes ownership visible, gives policy one home, improves testability, and lets the application reject invalid configuration early. It also reduces the risk that a new caller quietly depends on a setting, log format or persistence behaviour that no one regards as a supported contract.

Configuration values should be bound into typed options or dedicated singleton configuration objects. Logging should use the app-specific logging contract where one exists, while still allowing the implementation to delegate to the platform logger and OpenTelemetry exporters.

The Twelve-Factor App states: ÔÇ£Store config in the environment.ÔÇØ This is a useful reminder to separate deploy-time configuration from code; it does not remove the need for typed options and validation inside the application.

**Sources:** [The Twelve-Factor App, Config](https://12factor.net/config); [Microsoft, Options pattern](https://learn.microsoft.com/en-us/dotnet/core/extensions/options); [Microsoft, logging in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging).

## 8. Use structural contracts where they carry meaning

A model interface is justified when it expresses a capability consumed by something else: identity, title, description, lifecycle, classification, ordering, validation or another generic rule. Such contracts make schema generation, object mapping, validation and policy checking more predictable because the consumer can rely on a named capability instead of repeated type-specific discovery.

Do not add marker interfaces without a consumer. A contract should improve substitution, generic constraints, infrastructure discovery, validation, mapping or another identifiable behaviour.

**Sources:** [Eric Evans, Domain-Driven Design](https://www.domainlanguage.com/ddd/), which established the vocabulary of entities, value objects, repositories and bounded contexts; [Microsoft, DDD-oriented microservice](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice), which describes layering and domain model boundaries; [ISO/IEC/IEEE 42010](https://www.iso.org/standard/74393.html), which treats architecture as the structure and relationships relevant to stakeholder concerns.

## 9. Model different concerns at different levels

The ANSI/SPARC three-schema idea remains useful as a reminder that external views, a logical model and a physical storage model are different concerns. It should not be copied mechanically, but it helps explain why a controller contract, application DTO, domain model and persistence record need not be the same type.

Likewise, domain-driven design distinguishes the domain model from application orchestration and infrastructure implementation. Keep the mapping explicit where the meanings differ; reuse a type only where the responsibility and lifecycle genuinely match.

**Sources:** [ANSI/SPARC three-schema architecture, overview](https://www.ibm.com/docs/en/ida/9.1.2?topic=design-three-schema-architecture); [Microsoft, DDD-oriented microservice](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice); [Eric Evans, Domain-Driven Design](https://www.domainlanguage.com/ddd/).

## 10. Make discovery deterministic and observable

Convention and reflection can remove repetitive registration and make extension predictable, but discovery is infrastructure, not magic. Define the assemblies or namespaces that may participate, make ordering deterministic, reject ambiguity, and report what was discovered. Test that design-time and runtime discovery produce the same result where generated schema, registrations or seed data depend on it.

Explicit registration remains appropriate when discovery cannot be made safe, deterministic or observable. The goal is controlled convention, not reflection for its own sake.

**Sources:** [Microsoft, dependency injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection); [Microsoft, .NET assembly loading](https://learn.microsoft.com/en-us/dotnet/standard/assembly/load); [ISO/IEC 25010 overview](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010), especially maintainability and reliability concerns.

## 11. Assemble lifecycle objects early through explicit contracts

Startup is the controlled composition root. Assemble configuration, policy, registry, schema, initialisation and pipeline responsibilities as cohesive objects at the earliest lifecycle phase that has enough information to construct and validate them. An object should own meaningful state, behaviour, invariants, policy or lifecycle; this is not a reason to wrap every scalar in a class.

Discover and invoke those objects through capability contracts rather than arbitrary names or loose delegates. This makes composition visible, supports deterministic ordering and failure handling, improves testability and replacement, and gives operators a structured account of what was discovered and executed. It also prevents lifecycle behaviour from becoming a trail of spare arguments passed between unrelated methods.

Reflection is useful when it is scoped, contract-led, deterministic and observable. Where those conditions cannot be met, use explicit registration. Contracts are only useful when their lifecycle methods are actually invoked, so startup tests must verify execution as well as discovery.

**Sources:** [Microsoft, dependency injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection) documents service composition; [Microsoft, options pattern](https://learn.microsoft.com/en-us/dotnet/core/extensions/options) documents typed configuration; [ISO/IEC 25010 overview](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010), especially maintainability, reliability and operability concerns.

## 12. Security and auditability are design inputs

Do not bolt security, privacy, authorisation, auditability or recoverability onto a working path later. Identify the protected resource, actor, permission, classification, state transition and audit event while the boundary is being designed.

A feature that is functionally correct but cannot explain who changed what, why access was allowed, or how failure is recovered is not complete for a serious service.

**Sources:** [NIST Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework), [OWASP Application Security Verification Standard](https://owasp.org/www-project-application-security-verification-standard/), and [ISO/IEC 25010 overview](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010).

## 13. Build the foundations before the feature

Configuration and settings, startup and lifecycle, and access and security control are foundational platform concerns. They are often misidentified as secondary work because they do not look like the business feature that motivated the project. In reality, they determine whether the feature can be assembled consistently, secured correctly, observed in operation, extended later and supported by people who did not write it.

The pressure to start with a visible business outcome is understandable. The risk is starting with only a trowel: a controller, a table and a happy-path service, without the contracts, composition rules and safety boundaries needed to build the high-rise around it. The result may demonstrate motion, but it cannot reliably carry the next floors.

The detailed construction and runtime order is maintained in the canonical [Holistic Development Architecture](../development/component-architecture.md). In summary, contracts establish ownership, base types preserve shared invariants, registries and services assemble capabilities, repositories or providers own effects, maps protect boundaries, vertical slices carry capabilities end to end, and horizontal flows coordinate those slices.

This is not ceremony for its own sake. It prevents a feature from becoming the accidental owner of platform concerns that later features must copy. It also gives a developer a way to see the system: vertical slices are the shafts that carry capabilities through the building; horizontal flows are the routes between rooms and capabilities; foundational contracts are the structure that makes both possible.

The foundation is not finished when its types exist. It is finished when the contracts are invoked in the intended lifecycle, access decisions are enforced, configuration and settings are distinguishable, mappings preserve safe boundaries, startup is observable, and the relevant failure paths are tested. A developer may begin a business slice before every future capability is known, but may not bypass these foundations merely to produce a faster demonstration.

**Related guidance:** [Startup and discovery](../foundations/startup.md), [Data protection](./data-protection.md), [Vertical slices](../capabilities/slices.md), [Horizontal flows](../capabilities/flows.md), and [IQueryable and governed queryability](../foundations/iqueryable.md).

## 14. Design the knowable; discover the novel

Enterprise application engineering is not an empty landscape. Across decades of platforms, the recurring structures are visible: boundaries, contracts, objects, mappings, application services, persistence policies, registries, startup lifecycle, identity, access control, audit, queryability and operational diagnostics. A team should observe those recurring structures, abstract their stable responsibilities, categorise the problem domain and design the formwork before asking developers to produce feature code.

The fact that the next screen or conceptual object is new does not make the whole system unknown. The novelty is usually concentrated in the domain vocabulary, the relationships that matter to this organisation, the decisions the users make and the presentation they need. Those parts can be explored and refined. The surrounding engineering discipline should not be rediscovered from scratch in every feature.

Design the complete intended structure even when implementation is staged. A deferred capability should still have a named place, stewarding boundary, contract, dependencies, lifecycle, security posture, persistence intention and relationship to the relevant slices and flows. Deferred means "understood and not yet built"; it does not mean "left for a future team to invent from fragments."

This is a waterfall of the knowable and an agility of the unknown:

- establish the known boundaries, contracts, lifecycle, security model, mapping rules, persistence conventions and operational foundations;
- build the first real vertical slice through those foundations;
- use the slice to test the abstractions against a genuine domain capability;
- refine the conceptual model and presentation as understanding improves; and
- add horizontal flows when real capabilities need to coordinate.

This is not permission to invent arbitrary internals or build every future instance immediately. It is a refusal to confuse disciplined preparation with waste. YAGNI is a guard against speculative features and unused implementation; it is not a licence to omit a known contract and force every later developer to rebuild the same boundary under pressure.

## 16. Defer construction; preserve structure

When budget, sequencing or evidence prevents a capability from being built now, preserve its intended place in the architecture. Define the arch before postponing the rooms it will support. The design should make clear what is present, what is deliberately deferred, what contract will be honoured later and which assumptions must remain stable for the deferred work to fit.

Do not treat physical deletion as the default form of simplification. Removing a column, relationship, state, contract or persistence structure because it is not currently exercised can damage referential integrity, historical meaning, migration paths and future composition. In a relational system, punching out a structural brick may create failure that is not visible until another capability, report, migration or historical record relies on the position it occupied.

Deletion requires the same design discipline as addition: identify consumers, historical obligations, migrations, referential effects, security consequences, recovery implications and the replacement or retirement decision. If a part is known and structurally important but not yet affordable, prefer an explicit deferred design or inert extension point over an undocumented absence. The goal is not to carry unused behaviour forever. The goal is to prevent each generation from leaving an unplanned pile of partial structures for the next generation to interpret.

## 15. Reflection is evidence of organisation

Reflection does not organise a system. Contracts do.

Contract-based reflection is valuable because it demonstrates that the system has been organised before startup wiring is written in detail. A configuration object, schema definition, mapping participant, service, registry or lifecycle initialiser can be discovered because its contract identifies what it is, who owns it, when it runs, what it depends on and how it is reported.

Reflection is therefore a consequence of good organisation, not a panacea for poor wiring. A badly designed system can be wired explicitly or discovered reflectively and remain badly designed. Conversely, a well-designed contract system may use explicit registration while it is being established. The choice of wiring cannot repair missing ownership, ambiguous responsibilities or absent lifecycle semantics.

The test is not whether reflection feels sophisticated. The test is whether the contracts make discovery safe, bounded, deterministic, diagnosable and replaceable. If they do, reflection should normally remove repetitive host wiring. If they do not, improve the contracts and evidence before adding more discovery machinery.

## Applying the principles

A principle is not, by itself, a reason to add architecture. A proposed abstraction or infrastructure dependency becomes easier to judge when its connection to a principle or operational risk is clear, when the smallest boundary that addresses the risk is understood, and when its measurable cost is visible. The same explanation can describe how the choice will be tested and observed and what evidence would later justify removing or simplifying it.
