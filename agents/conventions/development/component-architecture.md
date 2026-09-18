# Holistic Development Architecture

This is the canonical portable guidance for developing repeatable, low-risk
capabilities. It explains how the same design discipline applies from the whole
service down through an LDM, domain, project, namespace, folder, type, model
contract, schema and mapping. Component placement is one visible consequence of the
whole development approach, not the subject in isolation.

Skills, repository bindings and cleanup workflows should link here rather than
restating these rules.

This document defines purpose, ownership, substitution and dependency direction. A
repository binding supplies the local project names, base classes, discovery
contracts and persistence helpers.

## Why the precision is deliberate

This is mature architectural formwork, not a blank canvas and not a demand that
developers surrender engineering judgment. The recurring structures of serious
software are already known: boundaries, contracts, substitution, separation of
concerns, model vocabulary, mappings, persistence policy, startup, security,
testing and operational evidence. Reusing those structures is how a team reduces
novelty, uncertainty and avoidable risk.

The freedom belongs in the parts that are genuinely new: the domain language,
relationships, decisions, policies, consumer needs and evidence. A developer may
qualify a pattern when the facts do not fit, but should explain the mismatch and
record the stronger alternative rather than silently inventing a local shape. The
precision is there to stop every feature and every agent from rediscovering the same
boundary decisions under delivery pressure.

## The holistic invariant

The system should be recognisable and repeatable at every scale:

```text
service
  -> LDM ownership boundary
    -> domain meaning
      -> project and assembly responsibility
        -> namespace and Domains/{Domain}/ folder
          -> purpose category and contract/implementation placement
            -> behavioural or structural contract
              -> model and property vocabulary
                -> extension-method schema
                  -> explicit mapper or query projection
                    -> vertical slice, flow and operational evidence
```

Each level answers the same questions:

- What meaning or responsibility is owned here?
- What contract may another part rely on?
- What may this part depend on, and what must it not know?
- Which implementation can be substituted without changing the consumer?
- What evidence proves the boundary, behaviour, data and failure policy?

This repeatability reduces novelty. Developers do not need to invent a new shape
for every feature, and agents can search for the same responsibility in the same
place. The remaining novelty is concentrated where it belongs: the domain language,
relationships, decisions, policies and consumer needs that are genuinely specific to
the capability.

## LSP and separation of concerns

The physical structure exists to protect SOLID principles, especially the Liskov
Substitution Principle and separation of concerns.

**Liskov substitution** means a consumer depends on a contract whose implementations
preserve the contract's stated meaning, preconditions, postconditions, failure
behaviour and security obligations. A repository implementation may change provider,
schema details or query mechanics without changing the repository contract. A
registry provider may be replaced without making callers know which implementation
was selected. A model that implements an established lock-state contract must honour
that capability; it must not present an inverse or contradictory meaning merely
because that is easier for one caller.

**Separation of concerns** means each level owns one kind of decision. An LDM owns a
coherent delivery and responsibility boundary. A domain owns meaning and rules. A
project owns a DDD-aligned physical responsibility. A namespace and folder expose
that responsibility to maintainers. A model contract owns a reusable capability or
property vocabulary. A schema extension owns the repeated persistence convention. A
mapper owns translation between representations. A controller, service, registry or
repository must not quietly absorb the decisions belonging to the others.

When a proposed shortcut crosses more than one of these responsibilities, treat it
as a design question rather than a convenience. Do not let a screen, endpoint,
database table, vendor API or first available project become the accidental owner.

When the pattern feels restrictive, ask which uncertainty it is removing. If the
answer is unclear, qualify the pattern with evidence. Do not discard a mature,
repeatable boundary merely because a one-off implementation would be shorter.

## Comments are part of the design interface

Code does not reliably explain its own purpose. Names and control flow show what a
program currently does; they do not necessarily explain why the responsibility is
here, which contract it protects, which neighbouring types participate, or which
apparently simpler alternative was rejected. Comments are therefore part of the
implementation interface for the next developer and for agents working from the
file in front of them.

Add a teaching comment or XML `<remarks>` block beside every new or materially
changed contract, base type, application service, repository, registry, mapper,
schema extension, adapter and security-sensitive branch when the surrounding code
does not make the design obvious. Explain, in plain language:

- the local objective and the problem it solves;
- the neighbouring contract constellation and a short data flow;
- which layer owns data, mapping, authorization, state, hydration, lookup or
  persistence;
- the invariant that must remain true; and
- the tempting alternative that was rejected, when the choice is not obvious.

Comments should not narrate assignments or repeat the method name. They should teach
the ownership and reasoning that would otherwise be lost. A longer comment that
prevents a future developer from inventing a parallel contract is valuable; brevity
is not a quality metric by itself. When a contract changes, update nearby comments
that describe the old ownership or data flow. Stale confidence is worse than visible
uncertainty.

## 1. Establish the physical boundary

An LDM (Logical Deployment Module) is a physical delivery and ownership boundary.
It groups a coherent purpose, its contracts, its data or external effects, its
operational responsibility and the changes that should normally be delivered
together.

An LDM is not automatically a DDD bounded context, an aggregate or one domain. An
LDM may contain several explicit business or technical domains. Keep those domains
visible instead of hiding unrelated meanings inside one generic project or namespace.

Before creating a type, record:

- the domain that gives the capability its meaning;
- the LDM that owns the capability and its public contract;
- the actor or system that uses it;
- the data, state, external effect and classification involved;
- the security, lifecycle, audit and failure obligations; and
- what is deliberately out of scope or deferred.

If ownership is unclear, stop at the boundary question. Do not let the first
controller, table, vendor client or UI component decide ownership by accident.

## 2. Make projects express DDD physical boundaries

Within an LDM, projects are physical boundaries for DDD responsibilities and
dependency direction. They are not merely convenient folders and they are not a
claim that every model is a different kind of conceptual model.

| Physical project responsibility | Owns | Must not own |
| --- | --- | --- |
| Shared or contracts | narrow LDM vocabulary, stable constants and cross-project contracts | persistence, UI behaviour or an unowned type dump |
| Domain | the ontological model, domain rules, state and repository ports | transport, ORM, vendor or startup implementation details |
| Application | use-case contracts, DTOs, orchestration, policy composition and projections | database queries that bypass repository policy or domain ownership |
| Infrastructure | technical services, registries, providers and adapters behind app-owned contracts | business meaning that belongs in the domain |
| Persistence infrastructure | repository implementations, physical persistence models, schema, migrations and seed policy | transport contracts or a second domain model |
| Presentation or app interface | protocol and consumer adaptation, endpoint metadata and transport models | domain rules, persistence access or authorization policy ownership |

The exact project names are a repository binding. The responsibility and dependency
direction are portable:

```text
Presentation or interface
            |
            v
Application contracts and orchestration
            |
            v
Domain model and domain contracts
            ^
            |
Infrastructure and persistence implementations
```

Outer projects may depend on inner contracts. Domain code must not depend on
transport, persistence providers, vendors or infrastructure implementations.
Shared remains narrow. If a type has one clear domain or layer owner, keep it there.

### 2.1 General folder grammar

The physical grammar inside a project is:

```text
Domains/
  {Domain}/
    {Purpose}/
      {local contract types}
      Implementations/
        {concrete types}
```

Common purpose folders include `Models`, `Services`, `Registries`, `Repositories`,
`Brokers`, `Controllers`, `Factories`, `Validators`, `Handlers`, `Maps`, `Mappers`,
`Extensions` and `Constants`. A project only contains the categories it owns. Do
not scaffold every category in every project.

Contracts are placed directly in the purpose folder and concrete implementations go
in its nested `Implementations/` folder. Model subcategories such as `Entities`,
`ValueObjects`, `DTOs`, `Requests`, `Responses`, `Commands`, `Queries`, `Options`,
`Projections`, `Records` and `Snapshots` may use the same nested implementation
pattern when the model type has a distinct contract or structural role.

The exact allowed categories for each physical project are a repository binding.
The binding may reserve special locations for persistence schemas, seeders, database
contexts or transport versions, but it must preserve the contract-before-
implementation grammar and the `Domains/{Domain}/...` ownership path.

## 2.2 Which folder do I use?

Choose the folder from the responsibility, not from the first class name that comes
to mind. These are the short teaching rules:

| Put it in | Use it for | Do not use it for |
| --- | --- | --- |
| `Controllers/` | Receiving a transport request, applying transport validation and invoking an application contract | Domain rules, persistence queries or substantial orchestration |
| `Services/` | One cohesive operation; an Application service coordinates a use case, while a Domain service holds domain logic that belongs to no one entity | Stateful participant lookup or direct controller-to-database shortcuts |
| `Repositories/` | A governed persistence port and its query/command capability in domain terms | In-memory definition lookup, generic collections or transport logic |
| `Registries/` | A stateful, usually singleton index of definitions, providers or participants that is assembled and hydrated at startup | A synonym for repository, a static bag, or every list of objects |
| `Models/` | Entities, value objects, records, DTOs, options and other representations with an identified semantic role | A miscellaneous dumping ground |
| `Maps/` or `Mappers/` | Declarative or procedural translation between representations, including provider-translatable projections | Business rules, persistence or external communication |
| `Extensions/` | Reusable contract or type-specific composition such as schema, validation or query helpers | Unrelated services hidden behind extension syntax |
| `Configurations/` or `Options/` | Typed values and policies supplied from configuration sources and validated before use | User-manageable runtime definitions that need their own lifecycle |

For the common persistent read capability, use the full vertical slice:

```text
Controller
  -> Application Service
    -> Repository contract
      -> Repository implementation
        -> DbContext or persistence provider
```

The application service composes the use-case query and DTO projection. The
repository owns the governed source, visibility and persistence restrictions. A
queryable read remains `IQueryable` until the approved boundary materialises it.
Controllers do not call repositories or `DbContext` directly.

For reusable definitions or providers that must be available throughout the running
process, use the registry path:

```text
configuration and/or database
  -> startup discovery and hydration
    -> registry contract
      -> singleton registry implementation
        -> application or infrastructure consumer lookup
```

The registry owns its lifetime, duplicate policy, lookup semantics, refresh or
replacement policy, thread-safety, diagnostics and failure behaviour. A registry may
be hydrated from the database, configuration, or both, but it is not a second source
of truth unless its contract explicitly says it owns a durable definition. If the
consumer needs a governed query over persisted records, use a repository instead.

When both paths are present, keep their roles distinct: a repository reads durable
state; a registry prepares reusable runtime participants from an owned source.

## 2.3 Discoverable definitions, groups and metadata

An implementation has more than one kind of discoverable object. Do not treat an
individual-definition marker as a vague synonym for "anything found by reflection."
It is a contract-led lifecycle signal, and each definition family still needs its
own registry, metadata shape, seed policy and consumer boundary. Concrete marker and
group names belong in the repository binding.

### Individual definitions

An individual-definition contract marks a code-defined object as a singleton
lifecycle participant. Common families include scheduled work, capabilities,
telemetry, alert policies, licences, health checks and smoke tests. The repository
binding should enumerate its actual contracts and map them to their registries.

The marker does not decide where the object is consumed. A specialized registry such
as a metric, signal, alert-policy, scheduler or smoke-test registry discovers the
appropriate contract, validates duplicates and ordering, and exposes the resulting
singleton collection to its consumers. Do not put every individual definition into
one universal registry merely because the reflection mechanism is shared.

### Group definitions

An individual-definition-group contract marks a container of related definitions.
Common families include capabilities, host configuration, user settings,
permissions, notifications and default grants. The repository binding should
enumerate its actual group contracts.

The group marker gives startup a common discovery boundary, but the specialised group
contract determines the next step:

```text
group contract
  -> family-specific attributes and members
    -> family-specific discovery projection
      -> family registry/provider
        -> optional persisted catalogue or seed reconciliation
```

Do not make a parent generic configuration or settings object reference every child
provider type. A specialized child may live in another assembly. The parent and child
are joined by the declared metadata and discovery contract, not by a compile-time
dependency from the generic parent to every provider assembly.

### Configuration groups

The configuration-group contract represents host or infrastructure configuration that is
bound during startup and normally registered as a hydrated singleton. The class is
described by a group-description attribute, whose path, title and description
describe the configuration group. Each public leaf property is described by a
property-description attribute, which supplies rendering,
mutability, display, default-value and display-security metadata. The property name
normally supplies the final configuration path segment.

The discovery process scans eligible assemblies for attributed configuration groups,
recursively follows attributed nested group properties, derives paths from the parent
path plus the CLR property name, validates nested path agreement, binds instances
from host configuration and registers them as singleton services. Independently
rooted provider configuration objects can live in another assembly and be discovered
without the generic parent knowing their concrete type. However, the current
configuration-group implementation does not provide the settings model's
attribute-only `ParentType` link for a child that is absent from the parent's CLR
object graph. A genuinely decoupled parent-to-provider-child composition would need
an explicit contract and migration decision; do not assume it exists merely because
both types have group attributes. The registry and startup diagnostics retain the
metadata; ordinary application code consumes the typed configuration contract rather
than raw `IConfiguration` or framework options APIs.

### Settings groups

The settings-group contract looks similar to the configuration-group contract but has a different owner
and lifecycle. Settings describe user-, workspace- or scope-manageable choices and
are projected into setting-definition metadata. A settings-group description
attribute describes the group and a setting-property description attribute describes
each setting property. Nested groups may be connected through a parent property or through the
attribute's `ParentType` and key metadata, including when the child type is in a
different assembly. Discovery composes canonical paths, validates cycles and
duplicates, orders groups and leaves, and feeds the settings definition provider.

An implementation may also retain a parallel programmatic settings-declaration
contract for modules that provide definitions directly. It is not the same shape as
an attributed settings group, but both feed the same settings-definition provider
and persisted settings catalogue. The distinction must remain visible while the
legacy declaration path is migrated or deliberately retained.

### Permission groups

The permission group is related to the other definition groups but is not a settings
object. A permission container is described by a group-description attribute; its
public constant string fields are individually described by a permission-description
attribute. Discovery projects those attributed constants into permission-item
objects, normalizes their grouping and canonical keys, rejects
conflicting duplicates, registers them in the permission registry, and the runtime
permission catalogue seeder reconciles them into persisted permission definitions.

Permission metadata is therefore field-based rather than property-based. Do not
force permissions through configuration-property attributes or merge the permission
registry with the settings/configuration provider. Shared discovery machinery is
appropriate; shared lifecycle and authorization semantics are not.

### The family boundary is the safety boundary

The same reflection, attributes and startup phases may appear in several families,
but each family must answer separately:

- what is the authoritative source: code, host configuration, user settings or
  persisted records;
- whether the result is a runtime singleton, metadata catalogue or both;
- whether startup hydration is binding, projection, reconciliation or registration;
- which duplicates are equivalent and which are conflicts;
- which values are secrets or security-sensitive; and
- which consumers may see or mutate the result.

This is why the pattern is reusable without collapsing the concepts:

```text
configuration groups -> hydrated singleton configuration objects
settings groups      -> setting definitions -> settings provider/catalogue
permission groups    -> permission items -> permission registry/catalogue
other definition groups -> family-specific registry or seeding pipeline
```

## 3. Design in dependency order

The following is the default construction order inside an LDM. It is a dependency
order, not a demand that every file be created in one pass.

This order is precise because the dependencies are real: contracts establish the
meaning, base types preserve shared invariants, schemas encode repeated structure,
maps preserve representation boundaries and slices prove the reachable capability.
Skipping a step may be justified by evidence, but it should be a conscious
qualification with a recorded reason. Treating the order as needless interference
usually moves the uncertainty into a later migration, security defect, compatibility
break or duplicated implementation.

### 3.1 Contracts before implementations

Define the boundary contract before its implementation. A useful contract states:

- the capability and its owner;
- inputs, outputs and error semantics;
- identity, lifecycle and state-transition rules;
- authorization, classification and audit expectations;
- persistence or external-effect ownership;
- discovery or registration requirements; and
- the evidence that will prove it works.

Use an existing contract when the semantics match. Create a new contract only when
the distinction is real and it gives a caller, generic rule, discovery mechanism,
schema rule, mapping rule, validation rule or policy a meaningful dependency.

### 3.2 Contracts choose the vocabulary

Contract-driven design applies to more than service and registry interfaces. It also
applies to the properties and capabilities of entities, value objects, configuration
objects, persistence records and DTOs when those types participate in shared generic
infrastructure.

Before adding a property or capability, search the available contracts and base types
for the meaning you need. If `IHasLocked` is the established contract, implement and
use its contract-defined locked member. Do not create an inverse property such as
`IsUnlocked` merely because it reads conveniently at one call site. An inverse name
can reverse default values, validation, query filters, schema conventions and map
semantics.

Likewise, `Key`, `Name` and `Title` are not interchangeable spellings:

- `Key` is a stable identifier used for lookup, composition or reference-data identity;
- `Name` is a domain or technical name that may be human-readable but is not
  automatically a stable key; and
- `Title` is display-oriented wording and may change without changing identity.

Use the existing contract and member whose semantics match. Add a new contract only
when the meaning is genuinely different, then document the distinction and provide
the corresponding schema, mapping, validation or discovery support where required.

This rule applies across the type constellation:

```text
structural or behavioural contract
    -> model/entity/configuration/DTO property vocabulary
        -> contract extension methods
            -> persistence schema and indexes
                -> explicit maps and query projections
```

Do not flatten this into a rule that every DTO must implement every entity contract.
DTOs may intentionally shape a consumer view, but their property names and meanings
must be chosen deliberately. If a DTO translates a contract-defined capability, the
map must make that translation explicit and must not silently invent a second
semantic vocabulary.

### 3.3 Base types and structural contracts next

Add or reuse base types and structural contracts only after the capability contract
is clear. A base type should centralize a stable invariant, lifecycle or policy that
several real types share. A structural interface should express a capability that a
consumer actually uses.

Do not begin with a generic base class because a folder needs a file. Do not add a
marker interface without a consumer. Keep domain-specific meaning out of a platform
base type.

### 3.4 Then place the owned type families

Place types beside the boundary that owns them and distinguish their responsibilities:

- definitions and configuration describe something the system must know;
- registries own collected participants, lookup, duplicate policy, lifetime and
  startup visibility;
- services perform a cohesive operation without owning registry state;
- repository ports define governed persistence capability at the inner boundary;
- repository implementations sit in the persistence boundary and own provider use;
- DTOs and maps belong to the application or consumer boundary when their shape is
  use-case or transport specific; and
- flows coordinate slices but do not take ownership of their rules or state.

Registries are normally nearer the platform/application composition and startup
surface because they collect definitions or providers. Repositories are normally
nearer the bottom of a data slice because they own the governed source and provider
boundary. Do not force registries and repositories into one generic "service" folder.

## 4. Build schema through contracts and extensions

Persistence schema is developed after the model contracts and base types are known.
The schema vocabulary comes first: contract-driven extension methods define how
identity, auditability, lifecycle, keys, relationships, indexes, classification and
other recurring capabilities are represented. The schema is not the place where a
developer chooses a new property meaning after the model has been created.

An individual schema definition should then use this order:

1. the applicable entity base extension;
2. shared structural contract extensions the entity implements;
3. relationship and foreign-key extensions;
4. indexes, constraints and other policy extensions; and
5. primitive property extensions only for genuinely residual fields.

Do not hand-roll equivalent ORM fluent chains in callers when the extension seam
exists. The extension owns naming, comments, ordering, indexing, conversion and
validation. Schema discovery must be scoped to the owning LDM, deterministic and
observable. Design-time and runtime model inputs must agree.

The persistence model carries domain meaning into storage, but it may add storage
requirements such as keys, concurrency tokens and provider constraints. It must not
become the authority for the domain concept merely because it is the first class
that reaches a database.

## 5. Add mappings after the model and schema seam

Mappings are explicit translation boundaries, not decoration. Add them after the
source and destination responsibilities are understood and after the reusable schema
or model contracts are available. The preferred chain is therefore:

```text
Contracts -> Extension methods -> Schemas -> Mappers
```

The arrows do not mean that every DTO is a persistence model. They mean that stable
meaning is chosen once, reusable structural rules are applied consistently, storage
is derived through the schema seam, and consumer representations are translated
explicitly rather than re-inventing property names at each layer.

Maps must:

- name the source and destination ownership;
- map every intended property explicitly;
- preserve security, classification and lifecycle semantics;
- remain provider-translatable when they participate in a queryable read; and
- be validated during startup or focused tests.

The application owns use-case DTOs and projection policy. The persistence boundary
owns provider-specific translation. Do not expose persistence records or domain
entities merely to avoid writing a map.

## 6. Implement vertical slices end to end

A vertical slice is one complete capability and its test boundary. It contains the
contract, orchestration, domain rule, access policy, mapping, persistence or external
effect, failure behaviour and evidence needed for that capability.

There are two useful directions to keep distinct:

- **Runtime request direction:** interface -> application -> domain/repository
  contract -> repository or registry -> persistence/provider.
- **Construction direction:** stable contracts and base types -> model and schema
  seams -> repository or registry implementation -> maps -> application -> interface.

For a queryable read, the normal shape is:

```text
interface/controller
    -> application service composes the use-case query and DTO projection
        -> repository exposes governed IQueryable source and visibility policy
            -> persistence provider executes the approved expression
```

The repository owns the source and access restrictions. The application owns
projection, use-case composition and application policy. The interface owns
transport limits and protocol semantics. No layer bypasses this path with direct
DbContext access, unrestricted entity exposure or premature materialization.

For a registry-backed capability, the shape is different:

```text
interface/application operation
    -> registry contract and lookup policy
        -> registered definition/provider
            -> optional external or persistence effect
```

The registry may be discovered and assembled near startup. It is not a replacement
for a repository, and a repository is not a general registry. Preserve the
distinction when a capability uses both.

Writes and state transitions are explicit commands. Queryability for reads does not
justify accepting arbitrary object graphs or expression trees for writes.

## 7. Complete the slice with cross-cutting evidence

The pieces most often missed after the happy path are not extra folders. Before a
slice is considered ready, check:

- identity, authorization, classification and data disclosure;
- audit, logical deletion, retention and recovery;
- concurrency, idempotency, retries and transaction boundaries;
- startup discovery, registration, readiness and diagnostics;
- mapping validation and query cost limits;
- focused domain, application, persistence and endpoint tests;
- operational signals, failure handling and support information; and
- deliberate deferrals, migrations and compatibility obligations.

Security, lifecycle, startup and operations are design inputs. They are not a later
hardening pass and they do not belong in an anonymous "miscellaneous" service.

## 8. Coordinate horizontal flows last

A horizontal flow coordinates several already-owned slices into a journey such as
submit, assess, approve, publish or reconcile. Define it after the participating
slices and their contracts are clear enough to state hand-offs, permissions,
transactions, retries, compensation and recovery.

The flow owns orchestration. The slices retain their own data, rules, permissions,
state transitions and audit meaning. If a flow must reach inside a slice to make the
journey work, the slice contract is incomplete or the ownership is wrong.

## Review test

For every component, a reviewer should be able to answer these questions from the
code and its nearby guidance:

1. Which LDM and domain own this meaning?
2. Which project boundary owns this responsibility?
3. Which contract does the caller rely on?
4. Which base type or structural contract supplies a shared invariant?
5. Is this a definition, registry, service, repository, map, flow or adapter?
6. Which security, lifecycle and failure policy applies?
7. How does the capability travel through its vertical slice?
8. What evidence proves the path is safe, queryable where intended and operable?

If one answer is missing, record the design gap instead of hiding it in the first
class or folder that happens to be available.

## Related guidance

- [Developer architecture route](./guidance-for-developers.md)
- [Logical Deployment Modules](./ldms.md)
- [Project boundaries](./projects.md)
- [Development principles](../foundations/principles.md)
- [Development constraints](../foundations/constraints.md)
- [Queryable access](../foundations/iqueryable.md)
- [Vertical slices](../capabilities/slices.md)
- [Startup and discovery](../foundations/startup.md)
- [Agent workflow](../documentation/agent-workflow.md)

[Up: Development Conventions](./readme.md)