# Eclipse Coffee Builder website

This repository contains the Hugo source for the Eclipse Coffee Builder for Jakarta EE website.

The Phase 1 foundation uses the Eclipse Foundation Hugo Solstice theme as a pinned Git submodule. Its expected commit is recorded in `.theme-version`. The configured base URL is the reserved placeholder `https://example.invalid/`; it must not be replaced until Eclipse Webdev confirms the official publishing URL.

## Prerequisites

- Git 2.31 or newer
- Hugo Extended 0.144.2, as pinned in `.hugo-version`

Clone with the pinned theme:

```shell
git clone --recurse-submodules <website-repository-url>
cd coffeebuilder-website
```

For an existing clone, initialize the theme with:

```shell
git submodule update --init --recursive
```

Confirm that the theme is at the expected revision:

```shell
git -C themes/hugo-solstice-theme rev-parse HEAD
```

## Local development

Start the local server:

```shell
hugo server --disableFastRender
```

Create a production build with the same warning policy used by pull-request validation:

```shell
hugo --minify --panicOnWarning --cleanDestinationDir
```

Generated output is written to `public/` and is intentionally ignored. The validation workflow builds pull requests only and does not publish or deploy the website.

## Theme and branding

The site follows Eclipse Foundation Solstice/Neptune conventions. The project logo is intentionally not configured in Phase 1; the header and footer use configurable Eclipse Foundation asset URLs until Webdev/EMO confirms the approved project assets and final trademark attribution.

No analytics are configured.

