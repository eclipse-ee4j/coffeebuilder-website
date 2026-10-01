+++
title = "Getting Started"
+++

Eclipse Coffee Builder is currently available as a development snapshot. This guide builds the project from source, installs its artifacts in your local Maven repository, and uses the Jakarta EE Minimal Archetype to create an application you control.

> **Development snapshot:** The current development line is `0.1.0-SNAPSHOT`. No production release has been published. The commands below use artifacts installed locally from the current source tree.

## Prerequisites

| Tool | Requirement |
|---|---|
| Git | Required to clone the canonical repository. |
| Java | **Java 21 is required to build Coffee Builder itself.** |
| Maven | Required to build and install the complete Coffee Builder reactor. |

Generated projects have their own Java requirement: Jakarta EE 11 projects use Java release 21, while Jakarta EE 10 projects use Java release 17. Java 17 is not sufficient for building Coffee Builder itself.

## 1. Clone the source

```shell
git clone https://github.com/eclipse-ee4j/coffeebuilder.git
cd coffeebuilder
```

The repository is the source of both the Maven plugin and the minimal archetype.

## 2. Build and install the development snapshot

From the repository root, build and install the complete reactor:

```shell
mvn install
```

This builds the parent, runs the Maven plugin tests, generates and builds the archetype integration projects, and installs both Coffee Builder artifacts as `0.1.0-SNAPSHOT` in your local Maven repository.

## 3. Generate a Jakarta EE 11 web project

Run this command from the directory that should contain the new project:

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

This recommended starting point creates a WAR project using the Jakarta EE 11 Platform API and Java release 21. See the [Jakarta EE Minimal Archetype reference]({{< relref "/archetype" >}}) for every input, the Jakarta EE 10 example, and the exact generated structure.

## 4. Enter and build the generated project

```shell
cd jakarta11-web
```

Use the Maven Wrapper included in the generated project:

```shell
./mvnw verify
```

On Windows PowerShell or Command Prompt, run:

```powershell
.\mvnw.cmd verify
```

The build produces `target/jakarta11-web.war`.

## 5. Continue with a developer-owned project

The generated result is a normal Jakarta EE Maven project. Its `pom.xml`, application classes, resources, package names, and build remain visible and fully editable. Coffee Builder creates the foundation; it does not introduce a proprietary runtime or hide the generated source.

From this point, the locally installed Coffee Builder Maven Plugin can add focused capabilities through the generated project's Maven Wrapper. Its invocation has this form:

```text
./mvnw org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:<goal>
```

Continue to the [Maven Plugin page]({{< relref "/maven-plugin" >}}) for the plugin introduction. Individual goals are intentionally documented in a later phase.

