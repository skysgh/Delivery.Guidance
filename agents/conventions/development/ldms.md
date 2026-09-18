# Develop LDMs

Read this document only when creating, restructuring or managing a Logical Deployment Module or its project boundaries.

For the accessible explanation of the package boundary, read [Logical Deployment Modules](../../../IT/shared/reference/catalogues/ldms.md), [LDM Layers and Contents](../../../IT/development/layers.md) and [Guidance for System Design Architects](../../../IT/foundations/guidance-for-system-design-architects.md).

Systems are developed as a stack of discrete Logical Deployment Modules (LDMs), per stakeholder.

For the physical boundary and construction order inside an LDM, use the canonical [Holistic Development Architecture](./component-architecture.md). This page defines LDM responsibilities and project traits; it does not create a second ordering scheme.

### Sys

The bottommost LDM is referred to as the System LDM. Its purpose is to wrap dependencies on external libraries with app-specific services to protect the rest of the code from code rot or DLL hell.


## LDM components

The project names below are an example repository binding, not a universal naming requirement. The durable ideas are that an application is an `App`, that `Modules` keeps modular structure visible and that the module key identifies the specific entry point or responsibility. Do not replace the portable `App` and `Modules` reminders with an organisation name merely to make a project name sound specific. Adapt the names to the repository while preserving clear module ownership and dependency direction.

Each LDM is composed of 5 or more projects:

```
App.Modules.{key}.AppInterface.REST				# REST, OData endpoints
App.Modules.{key}.Application					# Coordinator of Infra & Domain
App.Modules.{key}.Infrastructure				# App specific service wrappers of external dependencies
App.Modules.{key}.Infrastructure.Persistence.EF	# Domain specific
App.Modules.{key}.Shared						# Shared constants and contracts
App.Modules.{key}.Domain						# non-tech entitities and repository contracts
```


## Project Traits

Shared:
- defines LDM-specific constants, including the unique key of the LDM, used to compose controller routes, permission names, storage locations and diagnostic categories;
- consumes lower-level platform constants but does not redefine them;
- provides the LDM vocabulary to Domain, Infrastructure, Application and AppInterface projects;
- define LDM specific contracts
Domain:
- references Shared for access to LDM specific constants and contracts.
- defines the ontological model of the relevant business or technical domain: an abstracted account of durable capabilities, concepts, relationships, states, policies and rules;
- is physically implemented in domain code while remaining logically independent of transport, storage, vendors and frameworks; within its problem space it carries the conceptual meaning of the domain rather than transcribing one current client, team, vendor or framework description;
- defines repository ports that are implemented within the repository's persistence implementation, such as `App.Modules.{key}.Infrastructure.Persistence.EF`; and
- must not make physical storage entities the definition of the business concept.
Infrastructure:
- references Shared for access to LDM specific constants and contracts.
- define app specific services that wrap external framework or 3rd party dependencies
  - ConfigurationService
  - DiagnosticsService
  - KeyVaultStorageService
  - MediaStorageService
Infrastructure.Persistence.EF:
- references Shared for access to LDM specific constants and contracts.
- implements the repository contract defined by the Domain project;
- owns physical persistence entities, ORM configuration, migrations and storage mappings; and
- must not make the persistence shape the definition of the business concept.
Application:
- references Shared for access to LDM specific constants and contracts.
- orchestrates domain behaviour through domain contracts and application ports;
- does not depend on infrastructure implementations merely to coordinate a use case; and
- defines application commands, queries, client/application conceptual models and read models where a use-case contract needs them;
- maps client and application views into the domain model, and maps domain meaning back into the form each consumer needs; and
- must not force the domain model to mirror a current UI, organisational chart or local business process.
AppInterface:
- references Shared for access to LDM specific constants and contracts.
- adapts a UI or API boundary to application contracts;
- owns transport and presentation models and maps them at the edge; and
- does not move domain rules into controllers, endpoint handlers or UI components.


## Onion dependency direction

The LDM projects are organised around dependency direction, not merely around folders:

```text
       UI or API interface
         |
         v
 Client/application concepts
         |
         v
 Domain implementation:
physical code expressing the logical ontological model
         ^
         |
  Infrastructure implements ports
```

The arrows mean that outer code may depend on inner contracts. Domain code must not depend on transport, persistence, infrastructure or framework details. Infrastructure may implement a repository or service port owned by the inner layers, but the inner layers should not reach outward to call the implementation directly.

`Shared` is a narrow vocabulary and contract package, not a fourth business layer or a dumping ground for types that have no clear owner. Keep its constants and contracts stable, scoped to the LDM and free from persistence or UI concerns.

An LDM is a deployment and ownership boundary. It is not automatically a DDD bounded context, aggregate or single domain. One LDM may contain one or more explicit domains when those domains are related and their deployment, ownership and contracts justify keeping them together. Keep the domain boundaries explicit. When the domains no longer share a coherent purpose, lifecycle or responsibility, consider a separate LDM rather than hiding the difference inside one project boundary.

Within a domain, use DDD building blocks where they fit the ontological model of the business or technical domain: entities have identity and lifecycle, value objects carry a complete value without independent identity, aggregates protect a consistency boundary, and domain services hold rules that do not naturally belong to one entity or value object. These patterns are tools for thinking, not lore to follow mechanically. The team must abstract from real business or technical needs and evidence so that the model can survive changes in organisation, process, vendor or infrastructure. Application code coordinates use cases around that model; it should not turn database records or vendor objects into the domain model by default.

Different model types and implementation positions must be kept distinct. Every layer contains physical code and physical models; the layers are not conceptual, logical and physical model types. Interface DTOs and view models are physical representations shaped close to the concepts and language that a consumer, role or business process recognises. Application and domain entities are physical representations shaped closer to the ontological and logical distinctions, relationships, states, policies and rules that the system carries forward. Persistence models are also physical representations: they carry the logical meaning as far as the persistence boundary requires, while adding storage-system requirements such as primary keys, foreign keys, indexes, concurrency tokens and provider constraints. A technical adapter may physically express how a vendor or framework exposes a capability. Mapping remains explicit wherever semantic proximity, lifecycle or responsibility differs.

Do not follow layering terminology as an authority by itself. Use it to protect the customer's long-term value. If the outer application view is copied directly into the domain, today's client arrangement becomes tomorrow's system constraint. If the domain is invented without evidence from real clients and stakeholders, it becomes elegant but untrustworthy. The team must do the harder work: abstract what is durable, name what is genuinely different and keep the mapping visible.

## Constant dependency direction

Constants should form a dependency tree, not a collection of unrelated string bags:

```text
platform constants
  -> LDM constants
      -> domain or capability constants
          -> composed route, permission, storage and diagnostic values
```

Platform constants define genuinely shared building blocks. Each LDM's Shared project defines its own key and vocabulary. A domain or capability adds only values within its boundary. Controllers, policies, storage configuration and tests consume the composed constants rather than repeating literals.

This direction makes the LDM boundary visible and prevents higher layers from creating a second spelling for an existing value. Constants remain for immutable shared meaning. Runtime configuration, settings, secrets, persisted reference data and environment-specific locations belong in their appropriate typed contracts or data models.


## Code Organisation Conventions

- Infrastructure code is determined as one of the following:
- Services - Stateless singletons 
- Registries - Stateful singletons
- Repositories


## Maintenance

- If you come across projects that are still called App.Modules.{Key}.Interfaces[.*REST] rename to be `App.Modules.{Key}.AppInterfaces[.REST]`
- If you come across projects whose project names are correct, but the project folder name is not the same, update the folder name.





