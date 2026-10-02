+++
title = "Persistence and Data Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / Persistence and data

## Datasource declaration modes {#datasource-declaration-modes}

`add-persistence` and `add-datasource` accept the same datasource options. Their `declare` parameter controls where the datasource is configured:

| Value | Effect |
|---|---|
| `web.xml` | Adds a `<data-source>` entry to an existing `src/main/webapp/WEB-INF/web.xml`, using `java:global/jdbc/<name>`. |
| `class` | Generates an application-scoped `DataSourceProvider.java` with `@DataSourceDefinition`, using `java:app/jdbc/<name>`. |
| `asadmin` | Creates `post-boot-commands.asadmin` and adds that file to an existing Payara Micro plugin configuration, using `jdbc/<name>`. |

For `asadmin`, use `profile` to identify the Maven profile containing the Payara Micro plugin. Without that profile option, the goal searches the top-level build. The corresponding plugin configuration must already exist; [`add-payaramicro`]({{< relref "/goals/runtime" >}}#add-payaramicro) can establish it.

Datasource properties use comma-separated `name:value` pairs:

```shell
-Dproperties=ssl:true,schema:inventory
```

No escaping convention is defined by the current parser.

## add-persistence {#add-persistence}

**Category:** Persistence and data

Creates the persistence foundation, adds a persistence unit and datasource, and updates the target POM with the required APIs and detected JDBC driver.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 or 11 API dependency.
- For `declare=web.xml`, an existing `src/main/webapp/WEB-INF/web.xml`.
- For `declare=asadmin`, an existing Payara Micro build-plugin configuration at the selected build/profile level.
- A JDBC URL whose prefix is recognized by the embedded datasource catalog when driver and datasource-class configuration is required.

### Parameters

| Maven property | Type | Required | Default | Accepted values / meaning |
|---|---|---:|---|---|
| `datasource-name` | `String` | Yes | `defaultDatasource` | Java-identifier-like name beginning with a letter; letters, digits, and `_` thereafter. |
| `declare` | `String` | Yes | `web.xml` | `web.xml`, `class`, or `asadmin`. |
| `password` | `String` | No | — | Database password. |
| `persistence-unit-name` | `String` | No | `defaultPU` | Name written to `persistence.xml`. |
| `port-number` | `Integer` | No | — | Database server port. |
| `profile` | `String` | No | — | Maven profile used by `asadmin` mode. |
| `properties` | `String` | No | — | Comma-separated `name:value` datasource properties. |
| `server-name` | `String` | No | — | Database server host name. |
| `url` | `String` | No | `jdbc:h2:mem:test;DB_CLOSE_DELAY=-1` | JDBC URL used for catalog matching and configuration. |
| `user` | `String` | No | — | Database user. |

### Effects

- Creates or updates `src/main/resources/META-INF/persistence.xml` and its persistence unit.
- Uses persistence descriptor version 3.0 for Jakarta EE 10 and 3.2 for Jakarta EE 11.
- Adds the datasource declaration selected above.
- Adds Jakarta Persistence/CDI dependencies when absent and attempts to add the catalog-matched JDBC driver dependency.
- For Jakarta EE 10, also generates a CDI `PersistenceProvider.java` for `EntityManager` production.

Recognized URL families in the current embedded catalog are H2, HSQLDB, MySQL, PostgreSQL, Oracle, SQL Server, Derby, and MariaDB. The catalog supplies driver coordinates and the implementation then queries Maven artifact metadata for a version. If that lookup fails, the current goal logs the error and can finish without adding the driver; review the resulting POM. An unrecognized URL prefix does not produce a catalog-backed datasource class or driver dependency.

> **Current snapshot caveat:** The Jakarta EE 10 `PersistenceProvider` template currently fixes its `@PersistenceContext` unit name to `example-pu` instead of substituting `persistence-unit-name`. Review and align that generated value before building when the names differ.

> **Class-mode caveat:** The current `DataSourceProvider.java` template can fail while rendering datasource properties, with the goal logging the template error instead of failing the Maven invocation. Confirm that the provider file was actually created when using `declare=class`.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-persistence \
  -Ddatasource-name=inventory \
  -Dpersistence-unit-name=inventoryPU \
  -Ddeclare=web.xml \
  -Durl=jdbc:postgresql://localhost:5432/inventory \
  -Duser=inventory \
  -Dproperties=ssl:true,schema:inventory
```

## add-datasource {#add-datasource}

**Category:** Persistence and data

Adds or configures a datasource when the persistence foundation already exists. It is not an exact replacement for `add-persistence`: although the shared helper can initialize a missing descriptor, this goal does not establish all persistence/CDI dependencies or the Jakarta EE 10 provider source.

### Prerequisites

- An established persistence foundation. An existing `src/main/resources/META-INF/persistence.xml` and matching unit are the intended starting point.
- The declaration-mode prerequisites described for `add-persistence`.
- A catalog-recognized JDBC URL when driver and datasource-class configuration is required.

### Parameters

| Maven property | Type | Required | Default | Accepted values / meaning |
|---|---|---:|---|---|
| `datasource-name` | `String` | Yes | `defaultDatasource` | Java-identifier-like name beginning with a letter; letters, digits, and `_` thereafter. |
| `declare` | `String` | Yes | `web.xml` | `web.xml`, `class`, or `asadmin`. |
| `password` | `String` | No | — | Database password. |
| `persistence-unit-name` | `String` | No | `defaultPU` | Existing persistence unit to update. |
| `port-number` | `Integer` | No | — | Database server port. |
| `profile` | `String` | No | — | Maven profile used by `asadmin` mode. |
| `properties` | `String` | No | — | Comma-separated `name:value` datasource properties. |
| `server-name` | `String` | No | — | Database server host name. |
| `url` | `String` | No | `jdbc:h2:mem:test;DB_CLOSE_DELAY=-1` | JDBC URL used for catalog matching and configuration. |
| `user` | `String` | No | — | Database user. |

### Effects

- Creates or updates `persistence.xml` through the shared descriptor helper and makes the named persistence unit reference the datasource.
- Adds the selected datasource declaration and attempts to add the catalog-matched JDBC driver dependency.
- Updates `pom.xml`; it does not recreate the broader API/provider foundation supplied by `add-persistence`.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-datasource \
  -Ddatasource-name=reporting \
  -Dpersistence-unit-name=inventoryPU \
  -Ddeclare=web.xml \
  -Durl=jdbc:h2:mem:reporting
```

## add-entities {#add-entities}

**Category:** Persistence and data

Reads an entity-definition JSON file and generates persistence entities and their persistence-facing types.

### Prerequisites

- An existing persistence foundation, including `persistence.xml`.
- A valid entity-definition file. The current [entities example](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/entities.json) illustrates the accepted shape.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `entities-file` | `File` | Yes | — | Path to the JSON entity definition. |

### Effects

- Generates `<Name>Entity.java` under the derived `infrastructure.entity` package.
- Generates enum types under the derived `enums` package for fields declared as enums.
- Generates `<Name>EntityRepository.java` under `infrastructure.repository`, using the Jakarta Data repository contract.
- Processes configured fields, annotations, and entity relationships.
- Opens and saves the persistence descriptor but does not add dependencies to `pom.xml`.

The package root is derived from the target project's coordinates. Use [`add-domain-models`]({{< relref "/goals/application-model" >}}#add-domain-models) next when the application needs developer-facing models, mappers, repository contracts, and services.

### Example

From a project containing `config/entities.json`:

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-entities \
  -Dentities-file=config/entities.json
```

[View the persistence Mojo sources](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/persistence).
