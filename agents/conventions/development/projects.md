# Project Naming

Read this document when creating, renaming, restructuring or configuring projects and their namespaces.

For the accessible explanation of how project and deployment boundaries relate to responsibility, read [Logical Deployment Modules](../../../IT/shared/reference/catalogues/ldms.md), [LDM Layers and Contents](../../../IT/development/layers.md), [Deliverable Systems](../../../IT/shared/reference/catalogues/deliverable-systems.md), [Deliverables](../../../IT/shared/reference/catalogues/deliverables.md) and [Guidance for Developers](../../../IT/foundations/guidance-for-developers.md).

Use the canonical [Holistic Development Architecture](./component-architecture.md) for the responsibility and dependency direction behind the project names in this page. A repository binding may choose different names, but it must preserve those responsibilities.

Name each project so that its filename and default namespace identify its place within the system.

Use the intentionally generic `App.` prefix.

Follow the prefix with the Logical Deployment Module (LDM) key and architectural layer:

```text
App.Host
...
App.Modules.{LDM-Key}.AppInterfaces
App.Modules.{LDM-Key}.Application
App.Modules.{LDM-Key}.Domains
App.Modules.{LDM-Key}.Infrastructure
App.Modules.{LDM-Key}.Infrastructure.{Dependency}
App.Modules.{LDM-Key}.Infrastructure.Persistence.EF
App.Modules.{LDM-Key}.Shared
```

Use `AppInterfaces`, rather than the more usual `Interfaces`, for interfaces exposed to users (GUI) and applications (API).
The name intentionally places `AppInterfaces` alphabetically before `Application`.

## Project Configuration

Configure every .NET project to:

- generate XML documentation;
- enable nullable-reference-type analysis;
- disable implicit global using directives;
- enable all currently available .NET analyser rules;
- treat warnings as errors; 
- use warning level 9999
- enable all compiler warning waves available to the installed SDK.

Builds must fail at the first relevant compiler or analyser error. Do not allow
warnings to accumulate while feature work continues. A warning left behind becomes
background noise, hides new defects and eventually turns cleanup into a large,
expensive migration that nobody can review confidently. Fix the warning at the
change that introduced it, or record a narrow, justified suppression before moving
on.

XML documentation is a design-quality control as well as a compiler output. Public
types and members should be explainable in terms of purpose, ownership, contracts,
invariants and failure behavior. If a type cannot be described without hand-waving,
review its responsibility and boundary instead of hiding the confusion with shorter
syntax. Prefer explicit, readable code over compressed expressions and abbreviations
when the shorter form makes mistakes or ownership harder to see.

Apply the following configuration:

``` XML
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  <Nullable>enable</Nullable>
  <ImplicitUsings>disable</ImplicitUsings>
  <AnalysisLevel>latest-all</AnalysisLevel>
  <WarningLevel>9999</WarningLevel>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

## namespaces

Keep namespace roots as the project filename.


## Explicit Types

Enforce Prefer explicit local variable types over var because the visible type improves comprehension.

Enforce this through `.editorconfig`:

``` INI
csharp_style_var_for_built_in_types = false:error
csharp_style_var_when_type_is_apparent = false:error
csharp_style_var_elsewhere = false:error
```

This is a code-style rule. It discourages `var`; it does not remove the keyword from the C# language.

## Warning Resolution

Suppress a warning only when:

- its cause has been understood;
- the relevant behaviour is intentional;
- correcting it would make the implementation less accurate or maintainable;
- the suppression has the narrowest practical scope; and
- the reason is recorded beside the suppression.

Prefer a source-level suppression with a justification over a project-wide <NoWarn> entry.

Do **NOT** ever suppress warnings merely to obtain a successful build.

Do not defer a warning because the current total is already large. The correct
response to an existing warning backlog is a separately scoped cleanup campaign;
new work must not add to it or treat the backlog as normal project state.

## Warning Policy

Consider avoiding suppressing:

- `CS1591` ÔÇö A publicly visible type or member lacks XML documentation.
- `IDE0047` ÔÇö Parentheses are unnecessary according to the configured style.
- `NU5104` ÔÇö A stable NuGet package depends on a prerelease package. Use a stable dependency or publish the containing package as prerelease.
- `CA1816` ÔÇö GC.SuppressFinalize is missing or used incorrectly. Correct the disposal implementation.
- `CA1869` ÔÇö A JsonSerializerOptions instance is repeatedly created. Cache and reuse the instance unless its configuration genuinely varies.
- `NU1510` ÔÇö A direct package reference is unnecessary because the target framework already provides it. Remove the redundant PackageReference.
- `CA1805` ÔÇö A member is explicitly initialised to its default value. Remove the redundant initialiser.

Generally Ok to suppress:

- `CA1848` ÔÇö Logging uses ordinary ILogger extension methods instead of source-generated LoggerMessage methods. Prefer source-generated logging, particularly in shared or frequently executed code.
- `CA1873` ÔÇö A logging argument performs potentially unnecessary work when its log level is disabled. Pass uncomputed values, use source-generated logging, or guard the operation with IsEnabled. Suppress only when the argument is known to be inexpensive and readability would otherwise suffer.
- `CA1852` ÔÇö An internal type can be sealed. Seal it unless inheritance is intentionally supported, required by a framework, or likely within the designed extension model.


## Maintenance

If you encounter projects that do not adhere to the above, implement the above recommended strictness.

