+++
title = "Runtime Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / Runtime

Runtime goals add Maven profiles and plugin configuration. Their presence indicates configured behavior in the current snapshot, not a formal runtime support or certification statement.

## add-glassfish-embedded {#add-glassfish-embedded}

**Category:** Runtime

Creates or reuses a Maven profile and adds the embedded GlassFish Maven Plugin for running the project artifact.

### Prerequisites

- An existing Maven project that produces the application artifact referenced by the generated configuration.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `profile` | `String` | Yes | `glassfish` | Profile created or reused for the plugin configuration. |
| `port` | `int` | Yes | `8080` | HTTP port written to the embedded plugin configuration. |
| `contextRoot` | `String` | Yes | `${project.build.finalName}` | Application context root. Property name is case-sensitive. |

### Effects

- Adds `org.glassfish.embedded:embedded-glassfish-maven-plugin:7.0` to the selected profile.
- Configures the application as `${project.build.directory}/${project.build.finalName}.war`.
- Writes the selected port and context root and enables `autoDelete`.

The plugin version is hardcoded to `7.0`. This goal does not inspect the target's Jakarta EE version and does not select a GlassFish version from it. Current automated tests verify Mojo/helper interaction, not that every generated runtime combination starts successfully.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-glassfish-embedded \
  -Dprofile=glassfish \
  -Dport=8080 \
  -DcontextRoot=inventory
```

## add-payaramicro {#add-payaramicro}

**Category:** Runtime

Creates or reuses a Maven profile and adds the Payara Micro Maven Plugin, selecting a Payara Micro version from the target project's Jakarta EE version.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 (`10.0.0`) or Jakarta EE 11 (`11.0.0`) API dependency.
- A build that produces the artifact path referenced by the generated configuration.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `profile` | `String` | Yes | `payaramicro` | Profile created or reused for the plugin configuration. |

### Effects

- Adds `fish.payara.maven.plugins:payara-micro-maven-plugin:2.5.2` to the selected profile through a POM version property.
- Selects Payara Micro `6.2025.10` for Jakarta EE 10 and `7.2026.6` for Jakarta EE 11 from the embedded server catalog.
- Sets `deployWar` to `false` and adds `--autoBindHttp` plus `--deploy ${project.build.directory}/${project.build.finalName}` command-line options.

The current selection only recognizes the two exact Jakarta EE versions above. Automated tests verify version detection and configuration delegation; they do not establish a general runtime support promise.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-payaramicro \
  -Dprofile=payaramicro
```

For an `asadmin` datasource declaration, add this runtime profile before invoking [`add-persistence`]({{< relref "/goals/persistence-data" >}}#add-persistence) or [`add-datasource`]({{< relref "/goals/persistence-data" >}}#add-datasource) with the matching `profile` value.

[View the runtime Mojo sources](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo).
