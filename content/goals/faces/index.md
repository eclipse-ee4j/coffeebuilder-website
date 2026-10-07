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
| `overwrite` | `boolean` | No | `false` | Whether `add-faces` may replace an existing user-managed `index.xhtml` with the Coffee Builder-managed navigation index. |
| `welcome-file` | `String` | No | `index.xhtml` | Welcome file added when an existing `WEB-INF/web.xml` can be updated. |

### Effects

- Adds the Jakarta Faces API and CDI API dependencies when absent.
- Selects Faces API `4.0.1` for Jakarta EE 10 and `4.1.2` for Jakarta EE 11 from the embedded catalog.
- Generates `FacesConfiguration.java` under the derived `app.faces` package with Faces configuration and CDI application-scope annotations.
- Adds the configured welcome file to an existing `src/main/webapp/WEB-INF/web.xml`.
- Creates or preserves `src/main/webapp/index.xhtml` as the Coffee Builder-managed navigation index.

The goal does not add a servlet mapping. If `web.xml` is absent, the current helper skips the welcome-file update.

### Managed navigation index {#managed-navigation-index}

The managed `index.xhtml` provides an application-page list that Coffee Builder can update incrementally. Pages created by `add-face-page` and CRUD pages created by `add-forms-from-entities` are registered once; the index does not link to itself.

With the default `overwrite=false`, an existing `index.xhtml` that is not marked as Coffee Builder-managed is preserved, and automatic navigation updates are skipped. Use `-Doverwrite=true` only when you intend to replace that existing file with the managed index. An index already managed by Coffee Builder is retained and updated without this opt-in.

### Example

```bash
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-faces \
  -Dwelcome-file=index.xhtml
```

To opt in to replacing an existing user-managed index:

```bash
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-faces \
  -Dwelcome-file=index.xhtml \
  -Doverwrite=true
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
- Creates a reusable layout resource; it does not register the template as an application page in the managed navigation index.

### Example

```bash
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

```bash
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

- Adds the catalog-backed `org.primefaces:primefaces:15.0.17` dependency with the `jakarta` classifier when absent.
- Generates PrimeFaces CRUD `.xhtml` pages under `src/main/webapp`.
- Generates CDI managed beans under the derived `app.faces` package.
- Creates `src/main/resources/messages.properties`.
- References the pre-existing domain models and repository contracts in generated backing code.
- Registers each generated CRUD page in the Coffee Builder-managed navigation index, using the configured form title as its link label.

### Form field presentation {#form-field-presentation}

When a form field does not specify a component, Coffee Builder infers one from the corresponding entity field:

| Entity field | Generated PrimeFaces component | Behavior |
|---|---|---|
| Ordinary text or other scalar value | `inputText` | General text input. |
| Numeric primitive, wrapper, `BigInteger`, or `BigDecimal` | `inputNumber` | Numeric input. |
| `LocalDate` | `datePicker` | Date input using `yyyy-MM-dd`. |
| `LocalDateTime` | `datePicker` | Date and time input using `yyyy-MM-dd HH:mm`. |
| Enum | `selectOneMenu` | Options come from the generated enum values. |
| `manyToOne` relationship | `selectOneMenu` | Options come from the related domain repository. |
| `List<String>` | `chips` | Multiple string values. Other list element types are not supported by CRUD form generation. |

A field in `forms.json` can override the inferred component with `component`. Accepted component names are `inputText`, `textarea`, `inputNumber`, `datePicker`, `selectOneMenu`, and `chips`. Coffee Builder validates type-specific combinations and stops generation for an incompatible override.

This representative form definition overrides a string field while leaving the other components to inference:

```json
{
  "IssueList": {
    "entity": "Issue",
    "title": "Issues",
    "fields": {
      "description": {
        "label": "Description",
        "component": "textarea"
      },
      "status": {
        "label": "Status"
      },
      "project": {
        "label": "Project",
        "displayField": "name"
      },
      "labels": {
        "label": "Labels"
      },
      "dueDate": {
        "label": "Due date"
      }
    }
  }
}
```

### Relationships and CRUD behavior {#relationships-and-crud}

For a `manyToOne` entity field, the generated form presents related domain objects in a selection menu. Set `displayField` in that form field to choose the related property shown to the user. When it is omitted, Coffee Builder uses the first related field available from `name`, `title`, or `id`. The chosen field must exist, and the related entity must define an ID.

Generated CRUD pages support creating and updating a model, deleting an individual model, and deleting selected models in bulk. Both deletion paths include user confirmation.

The current [entity](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/entities.json) and [form](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/examples/forms.json) examples illustrate paired inputs. They are not a substitute for the later full input-file reference.

### Example

```bash
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-forms-from-entities \
  -Dentities-file=config/entities.json \
  -Dforms-file=config/forms.json
```

[View the Faces Mojo sources](https://github.com/eclipse-ee4j/coffeebuilder/tree/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/faces).
