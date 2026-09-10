# Docs Site Publishing Specification

## Purpose

Defines how `docs/` is built and published as a GitHub Pages site using MkDocs + Material, with `docs/` as the single source of content for both the repo and the site, plus the one-time manual repo setting required to activate Pages.

## Requirements

### Requirement: MkDocs + Material Site Configuration

The repository MUST include an `mkdocs.yml` configured with the Material theme, native Mermaid diagram support (`pymdownx.superfences`), search, and a navigation tree covering every page under `docs/`.

#### Scenario: Config builds with strict mode

- GIVEN `mkdocs.yml` and the `docs/` tree
- WHEN `mkdocs build --strict` is run
- THEN the build completes with zero warnings and zero errors

#### Scenario: Mermaid diagrams render

- GIVEN a page containing a Mermaid code block
- WHEN the site is built
- THEN the Mermaid syntax is valid
- AND the block renders as a diagram, not raw text

### Requirement: Single Source of Content

`docs/` MUST be the sole source for both the repository's rendered markdown and the generated site; no content MAY be duplicated between `docs/` and any other location.

#### Scenario: No duplicated content directories

- GIVEN the repository file tree
- WHEN searched for markdown content outside `docs/` and `README.md`
- THEN no duplicate copies of architecture, ADR, or diagram content are found

### Requirement: Build and Deploy Workflow

A GitHub Actions workflow MUST build the MkDocs site and deploy it to GitHub Pages on pushes to the default branch, using pinned `mkdocs` and `mkdocs-material` versions.

#### Scenario: Workflow runs on push to default branch

- GIVEN a push to the default branch
- WHEN the workflow triggers
- THEN it installs pinned `mkdocs`/`mkdocs-material` versions
- AND runs `mkdocs build --strict`
- AND deploys the built site to GitHub Pages on success

#### Scenario: Workflow fails closed on broken links

- GIVEN a page with a broken internal link
- WHEN the workflow's build step runs
- THEN the build fails
- AND no deployment occurs

### Requirement: Manual Pages Source Activation

Because no workflow file can change repository settings, the change MUST record an explicit non-code task instructing a repo admin to set Settings → Pages → Source to "GitHub Actions"; this requirement is satisfied by documentation of the manual step, not by any file in the repo.

#### Scenario: Manual step is explicitly recorded

- GIVEN the change's task list
- WHEN it is reviewed
- THEN it contains a task to set Settings → Pages → Source to "GitHub Actions"
- AND that task is marked as manual/non-automatable
