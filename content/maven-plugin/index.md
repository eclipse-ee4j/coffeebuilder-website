+++
title = "Maven Plugin"
+++

The Coffee Builder Maven Plugin adds focused capabilities to an existing Jakarta EE Maven project. Each goal makes a bounded change—such as adding persistence, generating a Faces page, or configuring a runtime profile—while leaving the resulting source and build files editable by the developer.

> **Development snapshot:** The current plugin is `org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT`. Install the current Coffee Builder reactor locally before invoking it. No production artifact has been published.

## How it operates {#operating-model}

Coffee Builder works incrementally. Start with a Jakarta EE project, choose the capability you need, inspect the generated or modified files, and continue developing the application normally. The goals are complementary; no single prescribed sequence or complete application architecture is imposed.

The plugin is invoked directly from Maven, so the Coffee Builder plugin itself does not need to remain configured in the target project's `pom.xml`:

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:<goal>
```

Pass a goal option as a Maven user property:

```shell
-D<option>=<value>
```

For example, after creating a Jakarta EE project:

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-validation-api
```

Some goals intentionally add dependencies, build plugins, profiles, descriptors, or generated source to the target project. The [Goal Reference]({{< relref "/goals" >}}) lists each effect.

## Target-project expectations {#target-project-expectations}

- Run a goal from the root of an existing Maven project containing `pom.xml`.
- Use Java 21 and Maven 3.9.16, matching the requirements recorded in the current plugin descriptor.
- Goals that select Jakarta-specific configuration expect the project POM to declare a recognizable Jakarta EE 10 (`10.0.0`) or Jakarta EE 11 (`11.0.0`) API dependency.
- Web and Faces goals use the conventional `src/main/webapp` layout; some changes require an existing `WEB-INF/web.xml`.
- File-driven goals require the indicated JSON or OpenAPI input file.
- Review generated changes and keep them under the same source control and review practices as handwritten code.

Need a clean starting project? The [Jakarta EE Minimal Archetype]({{< relref "/archetype" >}}) creates a compatible foundation. Follow [Getting Started]({{< relref "/getting-started" >}}) to install the snapshot and build the generated project first.

## Capability areas {#capabilities}

| Area | What the goals add | Reference |
|---|---|---|
| Jakarta EE | Bean Validation API configuration | [Jakarta EE goals]({{< relref "/goals/jakarta-ee" >}}) |
| Persistence and data | Persistence configuration, datasources, entities, and repository interfaces | [Persistence and data goals]({{< relref "/goals/persistence-data" >}}) |
| Application model | Domain models, mapping, repository contracts, and services | [Application model goals]({{< relref "/goals/application-model" >}}) |
| Jakarta Faces | Faces setup, templates, pages, managed beans, and PrimeFaces CRUD screens | [Jakarta Faces goals]({{< relref "/goals/faces" >}}) |
| REST and OpenAPI | Server interfaces and models generated from an OpenAPI document | [REST and OpenAPI goals]({{< relref "/goals/rest-openapi" >}}) |
| Runtime profiles | Embedded GlassFish and Payara Micro Maven profiles | [Runtime goals]({{< relref "/goals/runtime" >}}) |

## An incremental workflow {#workflow}

The branches below are options, not mandatory stages:

```text
Jakarta EE project
├── add-persistence
│   └── add-datasource
├── add-entities
│   └── add-domain-models
│       └── add-forms-from-entities
├── add-faces
│   ├── add-face-template
│   └── add-face-page
├── create-openapi
└── runtime configuration
    ├── add-glassfish-embedded
    └── add-payaramicro
```

`add-validation-api` can be used independently when a project needs the Bean Validation API dependency.

## Configuration model {#configuration-model}

Normal plugin execution reads version and template catalogs embedded in the plugin JAR. It does not require a remote configuration service.

Some current goals separately consult Maven artifact metadata to resolve a dependency or build-plugin version. Those lookups require repository access even though the Coffee Builder configuration catalogs themselves are local.

An opt-in development mode can follow configuration from the repository's `develop` branch. That mode is intended for contributors testing configuration changes, not for the normal user workflow. Coordinate such work through the [Community and Contributing page]({{< relref "/community" >}}).

## Next step

Use the [Goal Reference]({{< relref "/goals" >}}) to choose a goal, confirm its prerequisites and parameters, and see the files and POM changes it makes.

