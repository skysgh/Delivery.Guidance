# Engineering for People

Software engineering is the work of making a service understandable, dependable and able to change. The visible feature is only one part of that work. A service also needs clear responsibilities, boundaries, security, persistence, recovery and evidence that it behaves as intended.

## For meaningful applications

This material is for software that matters to people and organisations. It is for applications that carry work, records, access, decisions, money, obligations or public trust, and whose failure may damage the reputation of the organisation behind them. It is not written for disposable trinkets or proof-of-concept demonstrations. Those can be useful forms of exploration, but they are different engineering contexts and should be treated as disposable rather than allowed to become production by accident.

The guidance therefore does not begin with "MVP" or "simplicity first" as its governing standard. An MVP can be a valid scope decision, but it is not a quality standard. A smaller first release may defer capabilities while still being valuable, secure, usable, maintainable, supportable and recoverable. It must not defer the foundations needed to protect people, preserve meaning, operate the service or change it safely. This matters especially when funding may end: a team cannot responsibly assume that a later phase will repair choices that are unsafe or expensive to replace now.

When the vocabulary is new, [Finding a Useful Starting Point](./foundations/ways-into-guidance.md) begins with familiar situations and connects the question with a useful responsibility, catalogue or checklist.

A delivered outcome is wider than the service code. [Deliverables](./deliverables/readme.md) explains how software, supporting material, operational responsibility and evidence become one usable delivered service. [Deliverable Systems](./shared/reference/catalogues/deliverable-systems.md) describes the distinct systems around that service, while [Deliverables](./shared/reference/catalogues/deliverables.md) describes the data, content, domains, DNS, certificates, discovery, dependencies and operational evidence they require. [Registries](./shared/reference/catalogues/registries.md) distinguishes enterprise-wide sources that projects refer to from project-specific records that projects produce and maintain.

The benefit to people of this structure is explained in [Liberation Through Clarity](./shared/liberation-through-clarity.md), including what it gives developers, Business Analysts, Product Managers, Product Owners, architects, technical leads, testers, operators and maintainers.

Role-specific responsibilities are collected in [Stakeholders](./stakeholders/readme.md). They explain contributions and handoffs without splitting shared engineering concepts into separate silos. The [system perspectives](./deliverables/systems/readme.md), [foundations](./foundations/readme.md), [development material](./development/readme.md), [examples](./examples/readme.md), [catalogues](./shared/reference/catalogues/readme.md) and [checklists](./shared/reference/checklists/readme.md) offer other useful views of the same delivery.

Not every service needs the same amount of structure. A short-lived script, a tool used by one team, a shared service and a public-facing system have different audiences and different consequences when something goes wrong. The construction effort should match those consequences.

That creates a design problem before it creates a coding problem. People need a shared picture of what the service is responsible for, which parts depend on one another and what must remain safe when the next feature arrives. Without that picture, each contributor can make a locally sensible decision that leaves the whole service harder to understand and change.

## Protect the meaning before building the parts

The most important thing the team must protect is the meaning of the work. A business request says what people need. The service then needs to decide what that request means as a capability, a responsibility, a state and a relationship before a screen, class or database table is allowed to define it by accident.

Without that step, a system can still compile, deploy and pass its first tests. It may even look successful. But its value is in danger because the business meaning has become trapped inside the first implementation. A later change, report or integration must then work around yesterday's screen or storage design. The system is no longer carrying the business meaning clearly; it is carrying a history of local guesses.

This is why the distinction between **conceptual**, **logical** and **physical** design is not an optional architecture lesson. It is a shared protection for the whole team. The conceptual view says what the organisation means and needs. The logical view says what the service must represent and do. The physical view says how code, transport, storage and infrastructure will make that happen. In this guidance, the logical domain model is also the **ontological model of the business domain**: the system's considered account of what kinds of things exist, how they relate, which states and identities matter and which rules remain meaningful when today's organisation changes. ANSI/SPARC gives the views formal names, but the team can use the simpler question: *Are we still building what the business meant, or have our implementation details started telling us what it means?*

Ontology may sound like a specialist word. Here it means the team's answer to "What is this business world made of, and what makes one thing different from another?" The answer must be grounded in real client needs and evidence, but it must not merely copy one current client's screen, team structure or process. That abstraction is what gives the system a chance to serve tomorrow's needs as well as today's.

Every role helps protect the answer. Business analysts make client meaning and important distinctions visible. Developers abstract that evidence into the ontological domain model before choosing physical structures. Testers check the outcome and notice when storage or framework structure has become the only description of the capability. Architects, security, operations and delivery roles add the constraints and evidence that keep the meaning dependable over time. No one role owns all three views, but the whole team is responsible for keeping them connected.

## Architecture is for the living system

Services, frameworks, storage, deployment and operations are the building work. They provide the concrete structure in which the system can run safely. That work matters, but it is not the whole purpose of architecture. The building exists for the people, decisions, records, relationships and activities that live inside it.

Architecture begins when the team takes a stated problem and asks what must exist for that problem to be understood and solved. What is a distinct thing? What makes it the same thing over time? What can happen to it? Which things relate to it? Who may act on it? What must be remembered, protected, proved or recovered? Each answer helps reveal an ontological element of the relevant domain.

Contracts then give those elements protected relationships. They say what one part may ask of another, what it may rely on and how failure is expressed. That makes the parts isolatable and testable. Services and infrastructure provide the dependable building around them: the routes, utilities, storage, assembly and maintenance that let the living system continue to work.

This is why problem decomposition comes before technical construction. A beautifully built system can still serve the wrong world if the team never recognised the right things inside the problem. The purpose is not to decorate engineering with philosophy. It is to make sure the concrete structure carries a useful, durable model of the lives and work that depend on it.

Domain thinking applies to the whole building, not only to the business rooms. Business domains describe the people, records, decisions and relationships the service exists to support. Technical and platform domains describe what the service needs in order to exist and remain dependable: configuration, identity, access, persistence, messaging, startup, diagnostics, caching, deployment and recovery. Each has concepts, boundaries, capabilities, contracts, lifecycles and failure modes. Each needs translation at its boundaries. Infrastructure is therefore not a domain-free basement; it is a set of technical domains whose meaning must also be understood and protected.

A building gives us a familiar way to see this. It brings many services together within shared constraints: water, power, shelter and access must work together so that people can use the building safely over time. It may contain private spaces, shared spaces and services that need to remain separate while still working as one whole. The [Building Metaphor](./shared/reference/building-metaphor.md) develops this comparison and connects it to software relationships such as boundaries, contracts, capabilities and flows.

The comparison becomes especially useful when we notice that a building can become more demanding in several different ways. These dimensions overlap, but they are not the same thing. A building can be large without being unusually complex, while a small building can be difficult to design because many services and responsibilities must fit together.

These four dimensions describe the conditions that increase responsibility. They are not a project plan, a list of roles or a definition of scope. The sections that follow introduce those other ways of organising the work.

## Four ways responsibility grows

The building image gives us four questions to carry into software: how large is the structure, how many relationships must work together, how long must it remain dependable, and what do people depend on it to do?

### Scale changes the structure

A small dwelling and a high-rise need different levels of planning and different services, but both still depend on sound structure, clear responsibilities and careful joining between parts. Larger buildings add more services and more repetition, yet the underlying concerns do not disappear.

### Complexity changes the relationships

Scale and complexity often grow together, but complexity is about how many different parts, specialists and interactions must work together. A small house may have a core of electrical, plumbing and heating work. A larger building may add elevators, fire alarms, access systems, climate control, emergency power, water storage and other services, each with its own maintenance and failure concerns. It may also need redundancy, such as more than one way to provide power, water or access when a part fails.

The same pattern appears in software. A service may begin with a small set of capabilities, then acquire identity, persistence, messaging, caching, monitoring, recovery, integrations and specialised security concerns. Each addition can be useful, but it also creates another relationship that must be understood and operated. Complexity is therefore not a reason to admire a larger design. It is a reason to make responsibilities, connections and evidence more deliberate.

### Time changes what must be prepared

A building is not successful merely because it was constructed well. Once people move in, it must remain secure, accessible and usable as people, needs, conditions and surrounding services change. Building establishes the structure; operating keeps that structure dependable over time.

The building timeline also explains why the first visible result can take time. Before the first resident arrives, much of the shared structure must already be in place. Plans may advertise future homes, shops or public spaces long before anyone can use them, and that can make the early work look like delay. There is a long interval between the first excavation, the foundations and the first slab that makes the shape of the building visible.

That early advertising may be necessary. Promises about future homes, shops or public spaces can help secure the financing needed to buy materials, pay builders and continue the work. The promise helps make construction possible, but it does not change the order in which the building has to become real. Plans are still plans while the foundations, walls and windows are being built.

Confusing a plan with a finished structure creates waste. Ordering curtains and paint before the walls and windows exist may feel like progress, but the dimensions, openings and design can still change. The result is money spent on items that may not fit, may need to be stored, or may have to be replaced. Software has a similar risk: agreeing on a future experience can help secure investment and direction, but building detailed features before their supporting boundaries are understood can create work that later has to be moved or discarded.

However, once the foundations are solid and the building services have been mapped, the work can accelerate. The intended routes and connection points are known: there is a planned place for each pipe, cable, duct and service to pass through the structure. Builders do not have to cut into completed work or stop to discover whether a new route can fit, and they do not need to reengineer the building every time another service is added. Slabs and floors can be added more quickly because they have something reliable to rest on.

Near the end, activity gathers again as windows, elevators, climate systems, fittings and tenant spaces arrive together. That rush is possible because the earlier structure created safe routes, shared services and enough room for many kinds of work to happen at once. Software has the same pattern: identity, data protection, persistence, boundaries, startup, monitoring and recovery may need attention before the first user, but they are what allow later capabilities and experiences to arrive quickly without each new contributor having to invent the building again.

The paint and flooring customers saw in the original advertising may go in last, and can be completed quickly over the final month because the building design is coherent. The things they did not notice simply work: stairs and elevators carry them through the building, air conditioning keeps the temperature right, hot and cold water are available, toilets flush and lights come on. Most of that engineering was invisible in the brochure and remains invisible to most people after they move in. It is the work behind the scenes that makes the visible experience possible.

### Use changes the consequence

Responsibility expands with the kinds of people and activities that depend on a building. A flooded house may inconvenience one household. A flooded apartment building affects many households. A flooded office tower can close shops, offices, parking and public spaces on the ground floor, affecting people who do not live there at all. A museum may hold collections that cannot be replaced, while a facility that processes medical records may support decisions with consequences for people's health and lives. The building itself may be only one part of the system, but the things that happen within it can make a failure much more widely felt.

Those consequences lead to another distinction: different responsibilities may need different roles at different phases. The next section introduces those phases and roles while keeping them separate from the stakeholders who depend on the result.

## Phases, roles and stakeholders

These four dimensions also explain why the work cannot belong to one person forever. Phases describe when responsibility changes. They are sequential, but they overlap: design decisions continue into development, development can begin before every design question is closed, and operation may begin while later parts are still being developed. Each phase needs different kinds of knowledge, even when a role has the same name in more than one phase.

During design and financing, owners, funders, architects, engineers and specialist advisers shape what the building is for, what it must support and what can responsibly be afforded. An architect may rely on builders as consultants because builders understand materials, construction methods, sequencing and practical constraints. Those consulting builders are not necessarily the builders who later work in mud or hang from slabs while putting up columns in the wind.

During development, construction and delivery specialists turn the design into a complex system of structure, services, connections and evidence. They make the foundations, install the routes, connect the utilities, test the boundaries and resolve the details that allow the building to function. In software, this is the development of the complex system that supports the business or service, not merely the creation of the visible experience customers were promised.

During operations, the business or service is offered from the building. Operators, tenants, customer-facing staff and support teams use the structure and its services to provide that experience. Their responsibilities are different from those of the people who designed or constructed the building, even though they may identify changes that later return to design or development.

Maintenance continues in the background while the building is occupied. Maintenance personnel clean, repair, inspect, renew and preserve the building and help keep tenants safe, comfortable and able to use it. Software maintenance has the same continuing responsibility: understand what people already rely on, make controlled changes, preserve compatibility and keep the service trustworthy.

The word "builder" can therefore be misleading when it is treated as one job title. The architect's consulting builder, the development specialist who assembles the complex system and the maintenance specialist who keeps an occupied building working are all building specialists, but they work at different scopes, scales and phases. The [Builders Metaphor](./shared/reference/builders-metaphor.md) names these different contributions without pretending that the names are sealed job titles. One person or team may contribute in more than one phase, but the responsibility they carry can still change with the phase.

Stakeholders are a separate concern. They are the people and organisations who use the building, depend on its services, own or fund it, regulate it or carry responsibility for what happens there. They are not automatically builders, even when their needs shape the design. A customer may describe the experience they need, while a builder makes the structure and services that allow that experience to work. Keeping stakeholders distinct from phases and roles makes it easier to ask who is affected, who is responsible for the work and when that responsibility applies.

## Sequence and Progress

Sequence is another part of the design. Design the knowable, build it in stages and discover the novel inside the structure. A service can be safe to extend without every capability being complete. The unfinished parts need a known place, a responsible boundary and enough information for the next people to continue the work.

Frustration can come from confusing progress with what is visible. Foundations, contracts, routes, tests and operating responsibilities may be taking shape even when no new screen or object is ready. Agility is not the fastest visible motion; it is the ability to learn and change without making later work more expensive. [Speed is not the same as agility](./foundations/design-before-build-and-wgf.md) explains this distinction.

## Scope

Not every building needs every possible service. Even a high-rise may not need a helipad, an indoor gym or a swimming pool. Those facilities may be appropriate for a hospital or a high-end development, while another building may be perfectly suitable for the people and purpose it was designed to serve without them. A building can be smaller, simpler or intended for a different income bracket and still be sound.

There is still a minimum. Building codes define some requirements, and common sense adds others: structure must carry its load, people must be able to enter and leave safely, essential services must work, and the building must not create avoidable problems for the people who depend on it. Scope can be reduced, but structural responsibilities cannot simply be wished away.

Funding can also change. A development may have to become shorter or leave some floors unfinished because the available money runs out. That can be an acceptable outcome when the foundations, shared services and boundaries remain sound. Life happens; a smaller building can still be useful and dependable for its intended purpose.

The opposite does not hold. Building a short structure on poor foundations does not produce the same outcome as building a smaller structure on sound ones. Poor foundations undermine every floor, service and future change above them. In software, a narrower service can be a responsible scope choice when its contracts, security, data protection, persistence, lifecycle, operation and responsible boundaries are sound. A smaller scope does not make weak foundations acceptable.

## Software and building have different conditions

Buildings and software have much in common, but software does not consistently provide the same preparation, incentives or independent assurance as the building professions. People may learn within one local team, vendor material may teach product use rather than whole-system design, and commercial pressure may favour visible speed over durable structure. Peer review, testing and security assessment help, but they are unevenly applied.

This matters because a service can carry records, money, access, decisions or essential services while receiving less whole-system engineering than its consequences require. [Why Software Needs Engineering](./foundations/why-software-needs-engineering.md) explores these differences and the practical responsibilities they create.

The obligations are also becoming clearer. Practices that once seemed ordinary, such as keeping authorisation decisions inside an application because that was how the team worked, or deleting records because storage was expensive, may now conflict with requirements for data collection, use, sharing, tracking, retention, audit and deletion. The exact duties vary by jurisdiction and sector, but pleading ignorance is no longer a dependable position when a service mishandles entrusted information. The deeper paper explains why these obligations need to shape design from the beginning.

## Where next?

The practical question is simple: what must be built now, what can wait, and what minimum structure must be in place so that a smaller scope does not become future invention or unsafe operation?

For the reasoning behind sequence and scope, [The Structure Before the Feature](./foundations/the-structure-before-the-feature.md) explains why contracts, responsibilities, lifecycle and dependencies should be understood before a visible feature spreads assumptions through the service.

The [foundation pages](./foundations/readme.md) continue that explanation through current problems, shared design and construction choices. The [reader development guidance](./development/readme.md) then explains LDMs, layers, contents, System LDM services, constants and contracts. The [example gallery](./examples/readme.md) makes the ideas concrete through ordinary operations and experiences. The [reference pages](./shared/reference/readme.md) keep the building picture, vocabulary and writing guidance available as the questions become more specific, while the [Client Palette](./deliverables/systems/client/palette/first-look.md) and [Common Flows](./foundations/flows.md) help readers recognise the parts and journeys inside a visible experience.
