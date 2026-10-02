+++
title = "Jakarta Faces Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / Jakarta Faces

## add-faces {#add-faces}

**Category:** Jakarta Faces

Adds Jakarta Faces and CDI dependencies appropriate to the project's Jakarta EE version and creates application configuration for Faces.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 or 11 API dependency.
- The conventional web layout when a welcome file is to be updated.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `welcome-file` | `String` | No | `index.xhtml` | Welcome file added when an existing `WEB-INF/web.xml` can be updated. |

### Effects

- Adds the Jakarta Faces API and CDI API dependencies when absent.
- Selects Faces API `4.0.1` for Jakarta EE 10 and `4.1.2` for Jakarta EE 11 from the embedded catalog.
- Generates `FacesConfiguration.java` under the derived `app.faces` package with Faces configuration and CDI application-scope annotations.
- Adds the configured welcome file to an existing `src/main/webapp/WEB-INF/web.xml`.

The goal does not add a servlet mapping. If `web.xml` is absent, the current helper skips the welcome-file update.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-faces \
  -Dwelcome-file=index.xhtml
```

## add-face-template {#add-face-template}

**Category:** Jakarta Faces

Creates a Facelets template containing named insertion points.

### Prerequisites

- A web project using the conventional `src/main/webapp` layout.
- [`add-faces`](#add-faces) is the practical setup step before generating UI files.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `name` | `String` | Yes | — | Template path relative to `src/main/webapp`; a leading `/` is removed and `.xhtml` is added if omitted. |
| `inserts` | `List<String>` | No | — | Names for generated `ui:insert` regions. Supply a comma-separated Maven list. |

### Effects

- Creates the named `.xhtml` template under `src/main/webapp`.
- Adds one `ui:insert` region for each supplied insertion name.
- Does not change `pom.xml` or generate a managed bean.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-face-template \
  -Dname=templates/main \
  -Dinserts=header,content,footer
```

## add-face-page {#add-face-page}

**Category:** Jakarta Faces

Creates a Facelets page and, by default, its CDI managed bean.

### Prerequisites

- A web project using `src/main/webapp`.
- Run [`add-faces`](#add-faces) first.
- When `template` is supplied, that template file must already exist; [`add-face-template`](#add-face-template) can create it.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `name` | `String` | Yes | — | Page path relative to `src/main/webapp`; `.xhtml` is added if omitted. |
| `managed-bean` | `boolean` | No | `true` | Whether to generate the page's CDI managed bean. |
| `template` | `String` | No | — | Existing Facelets template path used by the page. |

### Effects

- Creates the requested `.xhtml` page under `src/main/webapp`.
- When a template is selected, generates definitions for the template's discovered `ui:insert` regions.
- With `managed-bean=true`, creates a PascalCase `<PageName>Bean.java` under the derived `app.faces` package and references it from the page.
- Does not change `pom.xml`.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-face-page \
  -Dname=inventory/list \
  -Dtemplate=templates/main.xhtml \
  -Dmanaged-bean=true
```

## add-forms-from-entities {#add-forms-from-entities}

**Category:** Jakarta Faces

Combines entity and form-definition JSON files to generate PrimeFaces CRUD screens and their backing beans.

### Prerequisites

- A Faces-enabled web project; run [`add-faces`](#add-faces) first.
- Existing entity-layer output from [`add-entities`]({{< relref "/goals/persistence-data" >}}#add-entities).
- **Practical prerequisite:** existing domain models and repository contracts from [`add-domain-models`]({{< relref "/goals/application-model" >}}#add-domain-models). This goal references those types and does not independently create missing domain model types.
- Valid entity and form-definition files. If a form selects a template, that Facelets template must exist.

### Parameters

| Maven property | Type | Required | Default | Meaning |
|---|---|---:|---|---|
| `entities-file` | `File` | Yes | — | Path to the JSON entity definition used by the generated types. |
| `forms-file` | `File` | Yes | — | Path to the JSON form/page definition. |

### Effects

- Resolves a current `org.primefaces:primefaces` version through Maven artifact metadata and adds it with the `jakarta` classifier when absent.
- Generates PrimeFaces CRUD `.xhtml` pages under `src/main/webapp`.
- Generates CDI managed beans under the derived `app.faces` package.
- Creates `src/main/resources/messages.properties`.
- References the pre-existing domain models and repository contracts in generated backing code.

The current [entity](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/entities.json) and [form](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/forms.json) examples illustrate paired inputs. They are not a substitute for the later full input-file reference.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-forms-from-entities \
  -Dentities-file=config/entities.json \
  -Dforms-file=config/forms.json
```

[View the Faces Mojo sources](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/faces).
