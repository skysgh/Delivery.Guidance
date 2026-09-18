# Code Conventions

Apply these conventions when creating, reviewing, restructuring or refactoring code in a service client.

For the accessible explanation of the browser boundary and language decision, read [Selecting a Language and Framework](../../../IT/foundations/language-and-framework-selection.md).

## Purpose

Organise code consistently so that its structure, responsibilities and patterns are recognisable and predictable.

A developer should be able to infer:

- where code belongs;
- where related code will be found;
- what responsibility a type has; and
- how an unfamiliar part of the system is likely to work.

Consistency reduces the time and risk involved in understanding, navigating, reviewing and maintaining the system.

## Scope

Use TypeScript for front-end application development in the browser. Do not use TypeScript or its JavaScript output for production service-side code unless there is no adequate alternative and an explicit exception has been approved.

TypeScript is a development language and type-checking layer. It is compiled to JavaScript; the production runtime is therefore JavaScript in a browser or a JavaScript runtime such as Node.js. The resource policy applies to that runtime, not to the TypeScript syntax itself.

An exception for service-side TypeScript requires a representative benchmark against the supported .NET alternative, including CPU, memory, latency, throughput, startup, concurrency and operational maintenance. Record the reason, ownership, boundary and review condition.

Use TypeScript instead of untyped JavaScript for front-end code because its static analysis and structural type system expose many contract errors before runtime. This is a correctness and maintainability benefit; it does not make the generated runtime a different execution engine.

## Framework

Use Angular for the supported front-end application framework unless a repository binding explicitly authorises another framework.

## Principles

- Late retrieval reduces initial download and startup work.
- Persist durable data in an appropriate data store rather than browser memory alone.
- Keep browser bundles, network transfers, CPU work and memory use proportionate to the user task.
- Keep front-end contracts separate from service-side domain and persistence contracts.

Do not use legacy `var`; use `const` by default and `let` only when reassignment is
required. Prefer a visible type annotation when inference would hide an important
boundary or make the value's contract unclear. Always use opening and closing braces
for `if`, `else`, `for`, `for...of`, `for...in`, `while`, `switch` cases and similar
control-flow blocks, even for one statement. The extra lines protect later edits and
make ownership and nesting visible.

## Files and primary types

Place one primary TypeScript type per file: one exported class, interface, type alias,
enum or other independently reusable declaration. The filename must match that
primary exported type, using the repository's established casing convention. For
example, `workspace-selector.component.ts` contains the primary
`WorkspaceSelectorComponent`, and `t.base.configuration.ts` contains the primary
`TBaseConfiguration` when the repository uses its type-prefix convention.

Small private helper types may remain in the same file only when they exist solely
to support the primary declaration. Do not combine unrelated exported interfaces,
aliases or classes in one file; separate files make consumer contracts discoverable,
make ownership clearer and let cleanup tooling detect drift.

## Sources

- [TypeScript Handbook, TypeScript for JavaScript Programmers](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html): TypeScript adds a type system to JavaScript and reports type errors during development.
- [TypeScript Handbook, TypeScript for the Java/C# Programmer](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-oop.html): TypeScript's type system is erased when JavaScript is emitted, so TypeScript does not create a separate production runtime.
- [MDN, Introduction to JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Introduction): JavaScript is used in browsers and also has server-side runtimes.
- [Node.js, Introduction](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs): Node.js runs JavaScript on V8 and is designed around asynchronous I/O, but that does not remove CPU and memory costs.
- [AWS Well-Architected Cost Optimization](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html): resource consumption and cost are architectural concerns.


## patterns




### UX Flow patterns

A majority of UX Design Sequences can be decomposed as a series of commonly used patterns.

* Record Management
* Application
* Approval

Developing the framework for the patterns as reusable components reduces novelty and maintainance costs.

#### Record Management with BREA/ST 

An Orchestrator is used to request a data set from a Broker, the results of which are presented a List/Tree Presenter. 
Select informs the Orchestrator to use a Aggregate specific Broker to retrieve a single record which is rendered in a Read Presenter.
If the user decides to Edit the record, the Orchestrator informs the Broker to retrieve a VM for editing, which is presented in a Write Presenter.









