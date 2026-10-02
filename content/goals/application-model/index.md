+++
title = "Application Model Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / Application model

## add-domain-models {#add-domain-models}

**Category:** Application model

Reads the same entity-definition JSON used by `add-entities` and generates an application-facing model layer plus mapping and repository/service types.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 or 11 API dependency.
- A valid entity-definition file.
- In practice, run [`add-entities`]({{< relref "/goals/persistence-data" >}}#add-entities) first. The generated implementations refer to the entity and entity-repository types created by that goal.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `entities-file` | `File` | Yes | — | Path to the JSON entity definition. |

### Effects

For each configured entity, the goal generates:

- a domain model/DTO under `domain.model`;
- a MapStruct mapper under `infrastructure.mapper`;
- an abstract model repository and entity-specific repository contract under `domain.repository`;
- an implementation under `infrastructure.domain` that uses the generated entity repository and mapper;
- application service types where defined by the generator templates.

It also adds MapStruct configuration—including annotation processing—to `pom.xml` when absent, and adds the Jakarta Transactions API when needed. The current implementation resolves a MapStruct version through Maven metadata while it prepares that configuration, so first execution may require repository access.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-domain-models \
  -Dentities-file=config/entities.json
```

Use the same entity definition that was supplied to `add-entities`; differing inputs can produce incompatible layers. The current [entities example](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/entities.json) is a concise illustration, not a full input-file reference.

[View the current Mojo source](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/arch/AddDomainModelsMojo.java).
