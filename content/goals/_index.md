+++
title = "Goal Reference"
+++

This reference documents every user-facing goal in `org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT`. The inventory and parameters are derived from the current plugin descriptor and Mojo implementations.

> **Snapshot contract:** These commands use a locally installed development snapshot. Begin with [Getting Started]({{< relref "/getting-started" >}}), and review changes in the target project after each invocation.

## Goal index {#goal-index}

| Category | Goal | Purpose |
|---|---|---|
| Jakarta EE | [`add-validation-api`]({{< relref "/goals/jakarta-ee" >}}#add-validation-api) | Add the version-appropriate Bean Validation API. |
| Persistence and data | [`add-persistence`]({{< relref "/goals/persistence-data" >}}#add-persistence) | Establish persistence and a first datasource. |
| Persistence and data | [`add-datasource`]({{< relref "/goals/persistence-data" >}}#add-datasource) | Add or configure a datasource in an existing persistence foundation. |
| Persistence and data | [`add-entities`]({{< relref "/goals/persistence-data" >}}#add-entities) | Generate entities, enums, and entity repository interfaces. |
| Application model | [`add-domain-models`]({{< relref "/goals/application-model" >}}#add-domain-models) | Generate domain models and application-layer contracts. |
| Jakarta Faces | [`add-faces`]({{< relref "/goals/faces" >}}#add-faces) | Configure Jakarta Faces dependencies and application metadata. |
| Jakarta Faces | [`add-face-template`]({{< relref "/goals/faces" >}}#add-face-template) | Create a Facelets template. |
| Jakarta Faces | [`add-face-page`]({{< relref "/goals/faces" >}}#add-face-page) | Create a Facelets page and optional managed bean. |
| Jakarta Faces | [`add-forms-from-entities`]({{< relref "/goals/faces" >}}#add-forms-from-entities) | Generate PrimeFaces CRUD screens from entity and form definitions. |
| REST and OpenAPI | [`create-openapi`]({{< relref "/goals/rest-openapi" >}}#create-openapi) | Configure server-side OpenAPI generation. |
| Runtime | [`add-glassfish-embedded`]({{< relref "/goals/runtime" >}}#add-glassfish-embedded) | Add an embedded GlassFish Maven profile. |
| Runtime | [`add-payaramicro`]({{< relref "/goals/runtime" >}}#add-payaramicro) | Add a version-selected Payara Micro Maven profile. |

The plugin also contains Maven's generated [`help`]({{< relref "/goals/help" >}}#help) goal. It reports plugin metadata and is not an application-transforming Coffee Builder goal.

## Invocation {#invocation}

```bash
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:<goal> \
  -D<option>=<value>
```

Commands in this reference use Bash syntax. Maven property names are the exact names shown in each goal's parameter table.

Maven-injected project, session, and component fields are intentionally excluded from the parameter tables. Return to the [Maven Plugin overview]({{< relref "/maven-plugin" >}}) for the operating model and target-project expectations.

## Inputs and generated code {#inputs}

`add-entities` and `add-domain-models` share an `entities-file` input. See [generated model fields]({{< relref "/goals/application-model" >}}#generated-model-fields) for the current enum and list behavior. `add-forms-from-entities` combines that entity definition with a `forms-file`; its [form field presentation reference]({{< relref "/goals/faces" >}}#form-field-presentation) documents component inference and supported overrides. These sections cover the inputs relevant to the current goals rather than defining a complete input-file schema. Current sample files are available in the canonical repository's [plugin examples directory](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/coffee-builder-maven-plugin/examples).

Generated Java, descriptors, pages, and POM changes remain part of the target project and are fully editable.
