+++
title = "Jakarta EE Minimal Archetype"
+++

The Jakarta EE Minimal Archetype creates a small Jakarta EE Maven project with an explicit platform version, profile, module type, and REST application path. The generated source and build are ordinary project files owned by the developer.

> **Development snapshot:** The archetype is currently `org.eclipse.coffeebuilder:jakarta-ee-minimal-archetype:0.1.0-SNAPSHOT`. Build and install the current Coffee Builder reactor locally before using it. No production release has been published.

For the complete setup sequence, start with [Getting Started]({{< relref "/getting-started" >}}).

## Purpose

Use the archetype when you want a clean Jakarta EE foundation rather than a preselected application architecture. It generates:

- a Maven Wrapper and a focused `pom.xml`;
- a CDI `beans.xml` descriptor;
- a JAX-RS application and hello resource;
- WAR-specific web resources when `jakartaModule=web`.

## Archetype coordinates

| Coordinate | Value |
|---|---|
| Group ID | `org.eclipse.coffeebuilder` |
| Artifact ID | `jakarta-ee-minimal-archetype` |
| Version | `0.1.0-SNAPSHOT` |

These coordinates identify the locally installed development artifact.

## Accepted inputs

Accepted inputs describe what the current archetype descriptor permits. They are broader than the combinations exercised by automated integration tests.

### Project and selection parameters

| Parameter | Default | Accepted value for these commands | Meaning |
|---|---|---|---|
| `archetypeGroupId` | None | `org.eclipse.coffeebuilder` | Selects this archetype's group. |
| `archetypeArtifactId` | None | `jakarta-ee-minimal-archetype` | Selects this archetype. |
| `archetypeVersion` | None | `0.1.0-SNAPSHOT` | Selects the current development line. |
| `groupId` | None | A valid Maven group ID; when `package` is omitted, it must also be usable as a Java package | Sets the generated project's Maven group ID. |
| `artifactId` | None | A valid Maven artifact ID; choose a value that leaves letters or digits after package sanitization | Sets the generated project directory and Maven artifact ID. |
| `version` | `1.0-SNAPSHOT` | A valid Maven project version | Sets the generated project's Maven version. |
| `package` | The supplied `groupId` | A valid Java package | Sets the base Java package; the archetype appends a sanitized artifact-ID segment. |

The examples supply every project parameter explicitly so generation is non-interactive and repeatable.

### Coffee Builder parameters

| Parameter | Default | Accepted values | Effect |
|---|---|---|---|
| `jakartaProfile` | `full` | `core`, `web`, `full` | Selects the matching Jakarta EE API dependency. |
| `jakartaVersion` | `11.0.0` | `10.0.0`, `11.0.0` | Selects the API version and generated Java release. |
| `jakartaModule` | `web` | `web`, `ejb` | Selects WAR or EJB packaging and its Maven packaging plugin. |
| `apiBasePath` | `/api` | String; no descriptor expression restricts it | Sets the JAX-RS `@ApplicationPath`. Use an application path such as `/api` or `/rest`. |

### Version and Java behavior

| Jakarta EE input | Generated API dependency version | Generated Java release |
|---|---:|---:|
| `10.0.0` | `10.0.0` | 17 |
| `11.0.0` | `11.0.0` | 21 |

This table describes generated projects. Building Coffee Builder itself requires Java 21 regardless of the target version.

### Profiles

| Profile input | Generated provided dependency |
|---|---|
| `core` | `jakarta.platform:jakarta.jakartaee-core-api` |
| `web` | `jakarta.platform:jakarta.jakartaee-web-api` |
| `full` | `jakarta.platform:jakarta.jakartaee-api` |

### Module types

| Module input | Packaging | Module-specific behavior |
|---|---|---|
| `web` | `war` | Configures `maven-war-plugin` 3.5.1 and generates `index.html` plus `WEB-INF/web.xml`. |
| `ejb` | `ejb` | Configures `maven-ejb-plugin` 3.3.0 for EJB 4.0 and does not generate the web resources. |

Both module types currently receive the same CDI descriptor and JAX-RS Java classes. The EJB variant does not generate a sample session bean.

## Generated project structure

For `artifactId=jakarta11-web` and `package=com.example`, the Java package segment is sanitized to `jakarta11web`:

```text
jakarta11-web/
├── .mvn/
│   ├── jvm.config
│   └── wrapper/maven-wrapper.properties
├── mvnw
├── mvnw.cmd
├── .gitignore
├── pom.xml
└── src/main/
    ├── java/com/example/jakarta11web/app/resources/
    │   ├── ApplicationResource.java
    │   └── HelloResource.java
    ├── resources/META-INF/beans.xml
    └── webapp/                         # web modules only
        ├── index.html
        └── WEB-INF/web.xml
```

No test source template is currently generated. The wrapper is configured for Maven 3.9.16.

## REST hello endpoint

`ApplicationResource` applies the selected `apiBasePath`. `HelloResource` exposes `GET hello`, accepts an optional `name` query parameter, defaults that name to `world`, and returns a response body in the form `Hello <name>`.

With the default path, representative application-relative requests are:

```text
/api/hello
/api/hello?name=Ada
```

For web modules, the generated index page links to the hello resource. If `apiBasePath` begins with `/`, the page removes that leading slash when constructing its relative link.

## Artifact ID and package sanitization

The generated Maven `artifactId` and project directory keep the value you provide. For the Java package only, every character outside ASCII letters and digits is removed from the artifact-ID segment.

For example:

```text
package=com.example
artifactId=orders-service
generated package=com.example.ordersservice.app.resources
```

The post-generation script moves the generated source directory to match that sanitized package. Choose inputs that result in a valid Java package segment.

## Automated test scenarios

The current archetype integration suite generates and runs `mvn package` for exactly these fixtures:

| Fixture | Profile | Jakarta EE | Module | API path | Java release |
|---|---|---:|---|---|---:|
| `web-full-11` | `full` | 11.0.0 | `web` | `/api` | 21 |
| `web-profile-10` | `web` | 10.0.0 | `web` | `/api` | 17 |
| `ejb-core-10` | `core` | 10.0.0 | `ejb` | `/api` | 17 |
| `custom-apipath` | `web` | 11.0.0 | `web` | `/rest` | 21 |

These fixtures verify representative paths through the accepted inputs. They do not claim automated coverage of every profile, version, module, and API-path combination.

## Verified examples

First [build and install Coffee Builder]({{< relref "/getting-started" >}}) from the monorepo root.

### Recommended: Jakarta EE 11 web project

```shell
mvn org.apache.maven.plugins:maven-archetype-plugin:3.4.1:generate \
  -DinteractiveMode=false \
  -DarchetypeGroupId=org.eclipse.coffeebuilder \
  -DarchetypeArtifactId=jakarta-ee-minimal-archetype \
  -DarchetypeVersion=0.1.0-SNAPSHOT \
  -DgroupId=com.example \
  -DartifactId=jakarta11-web \
  -Dversion=1.0.0-SNAPSHOT \
  -Dpackage=com.example \
  -DjakartaProfile=full \
  -DjakartaVersion=11.0.0 \
  -DjakartaModule=web \
  -DapiBasePath=/api
```

```shell
cd jakarta11-web
./mvnw verify
```

On Windows, replace the final command with `.\mvnw.cmd verify`.

### Compatibility: Jakarta EE 10 web project

```shell
mvn org.apache.maven.plugins:maven-archetype-plugin:3.4.1:generate \
  -DinteractiveMode=false \
  -DarchetypeGroupId=org.eclipse.coffeebuilder \
  -DarchetypeArtifactId=jakarta-ee-minimal-archetype \
  -DarchetypeVersion=0.1.0-SNAPSHOT \
  -DgroupId=com.example \
  -DartifactId=jakarta10-web \
  -Dversion=1.0.0-SNAPSHOT \
  -Dpackage=com.example \
  -DjakartaProfile=web \
  -DjakartaVersion=10.0.0 \
  -DjakartaModule=web \
  -DapiBasePath=/api
```

```shell
cd jakarta10-web
./mvnw verify
```

On Windows, replace the final command with `.\mvnw.cmd verify`.

The Jakarta EE 11 example compiles with `maven.compiler.release=21`; the Jakarta EE 10 example compiles with `maven.compiler.release=17`.

## Source

Inspect the current [archetype module in the canonical repository](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/jakarta-ee-minimal-archetype) for the descriptor, templates, post-generation script, and integration fixtures.

