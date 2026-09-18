# Vertical Slices

Read this document only when a task creates or changes an end-to-end capability with its own contract, orchestration, state or test boundary.

For the accessible explanation of slices and their relationship to the flow palette, read [Common Flows](../../../IT/foundations/flows.md), [Deliverable Systems](../../../IT/shared/reference/catalogues/deliverable-systems.md), [Deliverables](../../../IT/shared/reference/catalogues/deliverables.md) and [First Look at the Client Experience](../../../IT/deliverables/systems/client/first-look.md).

Before naming a slice, identify the Domain that gives the capability its meaning and the Functions that belong within it. The reader [Guidance Glossary](../../../IT/shared/reference/glossary.md) explains these terms and the risk of polysemy.

Use [Holistic Development Architecture](../development/component-architecture.md) for the LDM, project, contract, schema, mapping and runtime/construction boundaries that a slice travels through.

## Purpose

This document explains how one capability travels through the building. A slice gives a team a recognisable vertical path instead of asking each feature to invent its own arrangement of screen, service, rules and storage.

## The short version

A slice is one complete capability. It may be a Request, an Evidence item, a Comment, a search result or another meaningful part of the system. The slice has a boundary, rules, protected data, storage or an external effect, and evidence that it works.

In technical language, this is called a vertical slice because the capability travels through several layers. The useful idea is that one team can follow the capability from the outside of the building to the state or result it produces.

A vertical slice is a complete capability that can be followed from an external boundary to the state it reads or changes. It is a unit of architecture, delivery and testing, not merely a folder or one class.

A slice normally includes:

- a boundary contract for the request, query or interaction;
- application orchestration;
- domain rules and state transitions;
- a persistence or external-system policy;
- mapping and validation;
- authorisation and audit decisions; and
- tests at the narrowest levels that prove the behaviour.

## Queryable slices

Apply the full [IQueryable and governed queryability](../foundations/iqueryable.md) guidance to every .NET server-side read surface.

A read path should remain queryable from its public contract to the persistence policy where the platform supports it. Repositories expose the governed `IQueryable` source to application services, which compose the use-case query before the controller returns the boundary result. Each layer may restrict what can be queried, selected, expanded, ordered or paged. No layer may bypass the repository policy or expose unrestricted `DbContext` access merely to preserve queryability.

A write or transition path may share the same resource model as a read path, but it must make its command, validation, authorisation, transaction and state-transition rules explicit. Not every operation is CRUD. Create, read, update, submit, recommend, escalate, approve and reject are different behaviours even when they touch one record.

## How to recognise a slice pattern

The question is not whether one feature can be called a slice. The question is whether the same capability shape recurs across domains or resources strongly enough to deserve a named pattern. A candidate slice pattern has most of these properties:

- one primary capability or resource purpose;
- a contract that an actor, another module or a scheduled process can invoke;
- rules that belong to one stewarding boundary rather than to a coordinating journey;
- state, records or an external effect that the capability owns;
- a distinct authorisation, audit or data-protection decision; and
- a coherent test boundary from contract to owned effect.

It is probably a flow pattern when it mainly coordinates already-stewarded capabilities, passes information between them, decides what happens next, or spans several boundaries. A flow may call slices; a slice should not steward a flow's entire journey merely because it is the first step.

### Recurrence before abstraction

Look for **abstracted recurrence**: the same responsibilities, invariants, lifecycle shape, boundary contract or failure handling appearing in more than one capability. Also look for **recursive composition**: a pattern that can be applied at more than one level, such as a managed item containing other managed items, or a flow coordinating slices that each contain their own smaller internal steps.

These observations are evidence for a pattern, not permission to generalise immediately. Abstract only the stable invariant and the smallest useful contract. Keep names, rules and data that are genuinely domain-specific at the owning boundary. A useful pattern should reduce repeated decisions without hiding meaningful differences.

The practical test is:

1. Identify at least two real capabilities with the recurring shape.
2. Separate the invariant structure from incidental names and current implementation details.
3. Check that the proposed abstraction preserves ownership, security, data classification, lifecycle and failure semantics.
4. Confirm that a third plausible use would become clearer rather than more configurable and obscure.
5. Record unresolved variation as an explicit extension point or candidate, not as a pile of optional flags.

## Candidate slice families

These are common capability families, not a mandatory domain model. Adopt one only when the ownership, contract and lifecycle are coherent.

### Identity, access and participation

- **Identity profile**: represent a person, organisation, service principal or external identity without making an email address the identity key.
- **Access grant**: assign, evaluate, revoke or audit permission to a resource, capability or share.
- **Membership or affiliation**: manage a person's or organisation's participation in a workspace, group, programme or other owned collective.

Implementation: define the stable identity or subject reference, ownership and classification first. Expose read and command contracts through the application boundary. Centralise permission evaluation, record grants and revocations as auditable transitions, and test both allowed and denied paths. Do not bury access decisions in controllers or duplicate them in each consuming flow.

### Managed resources and reference information

- **Managed item**: create, read, update, classify, archive and lifecycle-manage a resource.
- **Reference or configuration item**: provide governed, often reusable values such as classifications, categories, settings or policy parameters.
- **Catalogue or offer item**: publish a discoverable item with suitability, availability, eligibility or presentation metadata.

Implementation: establish the resource owner, identity, classification, lifecycle states, transitions and query restrictions. Build the governed read surface first, then explicit commands for each meaningful transition. Keep reference data deterministic and separately governed from user-owned records. Do not turn every state change into a generic update.

### Relationships and intent

- **Relationship or association**: create, qualify, approve, suspend or end a relationship between two owned resources.
- **Selection or request**: record a consumer's intent against one or more offered resources.
- **Submission**: turn prepared information into a formal request for consideration.

Implementation: model both ends of the relationship and its authority. Define cardinality, duplicate handling, consent, effective dates, conflict rules and state transitions. Keep the relationship or intent as its own record when it has permissions, audit history, lifecycle or reporting needs. A flow may create it, but should not hide it in a transient orchestration object.

### Evidence, content and communication

- **Document or evidence**: receive, classify, version, review, replace, retain or withdraw an artefact supporting a capability.
- **Media or attachment**: manage binary content and its metadata, access, lifecycle and storage policy.
- **Notification or subscription**: record a recipient's subscription and deliver, suppress, retry or audit a notification.

Implementation: separate metadata from content storage and use scoped, short-lived access where appropriate. Define classification, retention, malware/content checks, versioning and audit events. For notifications, persist intent and delivery outcome separately, make delivery retry-safe, and never use notification text as an authority boundary.

### Discovery, records and exchange

- **Search or discovery projection**: expose safe, queryable discovery over owned records without becoming a second source of truth.
- **Audit or history record**: preserve who changed what, when, why and under which authority.
- **Import or export**: accept, validate, transform, produce and track a bounded data exchange.
- **Integration endpoint or synchronisation record**: represent an external contract, correlation, checkpoint, mapping or reconciliation state.

Implementation: make the source of truth explicit. Treat projections, exports and integration copies as governed data with classification and retention. Use idempotency keys, checkpoints, correlation, schema validation, quarantine and replay rules. An audit slice records evidence; it does not silently become a second editable domain model.

### Assessment and decision

- **Assessment**: evaluate evidence or a submission against rules and record findings.
- **Decision**: record an authorised recommendation, escalation, approval, rejection or other formal outcome.

Implementation: define the decision authority, evidence snapshot, separation of duties, conflict handling, reason codes, reversibility and audit trail. Keep assessment facts distinct from the decision outcome. A multi-step journey that gathers, assesses and approves is a flow across these slices.

## Implementing a slice

Use this sequence for a new slice:

1. Write the slice record: purpose, stewarding boundary, out-of-scope concerns, actors, data classification and external effects.
2. Define the boundary contract and error model before choosing tables, endpoints or UI components.
3. Define the resource and state model. Name valid transitions and reject invalid transitions explicitly.
4. Define the governed read surface: filters, projection, expansion, ordering, paging, visibility and maximum cost.
5. Define commands separately from reads. Put validation, authorisation, transaction and idempotency rules at the owning boundary.
6. Implement through the repository and application boundaries. Keep transport, domain and persistence models separate where their responsibilities differ.
7. Add audit, notification, integration, retention and recovery behaviour as part of the slice, not as later decoration.
8. Test the contract, denied access, invalid transitions, concurrency, persistence failure, retry behaviour and the successful path.
9. Record the slice's participation in flows without moving its rules into those flows.

## Slice record

Document each adopted slice with:

1. purpose and out-of-scope concerns;
2. owning actor and authorised actors;
3. resource, classification and state model;
4. read operations and query restrictions;
5. write and transition operations;
6. participating horizontal flows;
7. persistence, audit, retention and recovery requirements;
8. validation, idempotency and rule ownership; and
9. static, dynamic, contract, privacy and resilience test obligations.

Further reading: [Eric Evans, Domain-Driven Design](https://www.domainlanguage.com/ddd/), [Martin Fowler, Microservice Prerequisites](https://martinfowler.com/bliki/MicroservicePrerequisites.html), and [Microsoft, DDD-oriented microservice architecture](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice).

## Slice record

Document each adopted slice with:

1. purpose and out-of-scope concerns;
2. owning actor and authorised actors;
3. resource and state model;
4. read operations and query restrictions;
5. write and transition operations;
6. participating horizontal flows;
7. persistence, audit and recovery requirements;
8. validation and rule ownership; and
9. static and dynamic test obligations.
