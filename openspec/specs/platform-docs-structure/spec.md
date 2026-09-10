# Platform Docs Structure Specification

## Purpose

Defines the repository's documentation home: the root README, the `docs/` directory layout, and the navigation contract that ties them together so platform decisions are discoverable and citable by every app repo.

## Requirements

### Requirement: Repository README Overview and Map

The root `README.md` MUST describe the platform's purpose and MUST list every known repo in the ecosystem (Gardenia, Nexora, Sisques Account, account-api, account-web, Portero) with a one-line role description.

#### Scenario: README enumerates all known repos

- GIVEN the repository root
- WHEN `README.md` is read
- THEN it contains a repo map section
- AND every one of Gardenia, Nexora, Sisques Account, account-api, account-web, Portero appears with a description

#### Scenario: README links into the docs site

- GIVEN `README.md`
- WHEN its content is inspected
- THEN it links to the published docs site or the `docs/` entry point

### Requirement: Documentation Directory Structure

The repository MUST scaffold `docs/architecture/`, `docs/adr/`, and `docs/diagrams/` as top-level subdirectories under `docs/`, each containing at least one file.

#### Scenario: Required subdirectories exist

- GIVEN the repository file tree
- WHEN `docs/` is listed
- THEN `docs/architecture/`, `docs/adr/`, and `docs/diagrams/` each exist
- AND each contains at least one markdown or diagram file

### Requirement: Documentation Navigation Contract

Every markdown page under `docs/` MUST be reachable from the site navigation (no orphan pages), and the directory structure MUST map predictably to navigation sections (architecture pages under Architecture, ADRs under ADRs, diagrams referenced from the pages that use them).

#### Scenario: No orphan pages at build time

- GIVEN the full `docs/` tree
- WHEN the site is built
- THEN every markdown file is reachable from at least one navigation entry
- AND the build reports no orphaned-page warning

#### Scenario: Directory-to-nav-section mapping holds

- GIVEN a new file added under `docs/adr/`
- WHEN the site navigation is generated
- THEN the file appears under the ADRs section without manual placement elsewhere
