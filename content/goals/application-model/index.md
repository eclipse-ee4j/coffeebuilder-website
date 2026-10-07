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

### Generated model fields {#generated-model-fields}

Domain model field types follow the entity definition. In particular:

| Entity definition | Generated domain field |
|---|---|
| `"type": "enum"` with `values` | The enum type generated from the entity and field name. |
| `"list": true` | `List<T>`, where `T` is the configured field type. |
| `"type": "enum"` with `"list": true` | A list of the generated enum type. |

For example:

```json
{
  "Issue": {
    "fields": {
      "status": {
        "type": "enum",
        "values": ["OPEN", "CLOSED"]
      },
      "labels": {
        "type": "String",
        "list": true
      }
    }
  }
}
```

This produces a domain `status` field using the generated `IssueEntityStatus` enum and a `List<String>` `labels` field.

### Persistent identity {#persistent-identity}

When an entity definition identifies an ID field, its generated domain model uses that non-null ID for equality. Separate transient instances with null IDs are not treated as equal, and the model's hash remains stable when an ID is assigned.

### Example

```bash
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-domain-models \
  -Dentities-file=config/entities.json
```

Use the same entity definition that was supplied to `add-entities`; differing inputs can produce incompatible layers. The current [entities example](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/entities.json) is a concise illustration, not a full input-file reference.

[View the current Mojo source](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/arch/AddDomainModelsMojo.java).
