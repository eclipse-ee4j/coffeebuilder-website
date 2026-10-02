+++
title = "Jakarta EE Goals"
+++

[Goal Reference]({{< relref "/goals" >}}) / Jakarta EE

## add-validation-api {#add-validation-api}

**Category:** Jakarta EE

Adds the Bean Validation API dependency appropriate to the Jakarta EE version declared by the target project.

### Prerequisites

- An existing Maven project with a detectable Jakarta EE 10 (`10.0.0`) or Jakarta EE 11 (`11.0.0`) API dependency.

### Parameters

This goal has no user-facing parameters.

### Effects

- Adds `jakarta.validation:jakarta.validation-api` with `provided` scope if it is not already present.
- Adds the associated version property to `pom.xml`.
- Selects Validation API `3.0.2` for Jakarta EE 10 and `3.1.1` for Jakarta EE 11 from the embedded catalog.
- Does not generate Java source or descriptors.

### Example

```shell
mvn org.eclipse.coffeebuilder:coffee-builder-maven-plugin:0.1.0-SNAPSHOT:add-validation-api
```

The goal recognizes the two exact Jakarta EE platform versions above; another version is not silently treated as compatible.

[View the current Mojo source](https://github.com/eclipse-ee4j/coffeebuilder/blob/develop/coffee-builder-maven-plugin/src/main/java/org/eclipse/coffeebuilder/mojo/jakartaee/AddValidationApiMojo.java).
