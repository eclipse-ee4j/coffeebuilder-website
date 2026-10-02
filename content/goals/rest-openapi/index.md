+++
title = "REST and OpenAPI Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / REST and OpenAPI

## create-openapi {#create-openapi}

**Category:** REST and OpenAPI

Configures server-side Java generation from an OpenAPI document using the OpenAPI Generator Maven plugin.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 or 11 API dependency.
- A readable OpenAPI document. The default expects `openapi.yml` in the project root.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `openapi-server` | `File` | Yes | `${project.basedir}/openapi.yml` | Source OpenAPI document used for server generation. |

### Effects

- Copies the input document into the project root under its original filename.
- Adds `org.openapitools:openapi-generator-maven-plugin:7.23.0` with a `generate` execution.
- Uses the `jaxrs-spec` generator in Jakarta mode, configured for interfaces and MicroProfile OpenAPI annotations rather than a complete server runtime.
- Directs generated output to `target/generated-sources/openapi`.
- Derives the API package as `<project-base-package>.app.resources` and the model package as its `.model` child.
- Adds MicroProfile OpenAPI API `4.1` and the Jakarta-version-appropriate Validation API dependency.
- Creates local customization files `src/main/openapi-templates/pojo.mustache`, `src/main/openapi-templates/enumOuterClass.mustache`, and `.openapi-generator-ignore`.
- The ignore file suppresses generated `RestApplication.java` and `RestResourceRoot.java`, allowing the application to retain its own JAX-RS bootstrap.
- Resolves a build-helper plugin version through Maven artifact metadata and adds an `add-source` execution during `generate-sources`.

> **Current snapshot caveat:** The generated build-helper execution does not contain an explicit `sources` path, even though the OpenAPI generator writes to `target/generated-sources/openapi`. Review the resulting POM and generated-source compilation in the target project.

No API or model tests and no generated POM are requested by the embedded generator configuration. The goal establishes generation configuration; it does not configure or certify an application runtime.

### Example

With `spec/api.yaml` outside the project root filename collision:

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:create-openapi \
  -Dopenapi-server=spec/api.yaml
```

The repository includes a current [OpenAPI example](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/openapi.yaml).

[View the current Mojo source](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/rest/CreateOpenApiMojo.java).
