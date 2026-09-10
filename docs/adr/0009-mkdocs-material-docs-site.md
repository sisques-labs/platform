# ADR-0009: MkDocs + Material for the docs site

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

The `platform` repo needed a documentation site generator. The docs must
stay readable as plain markdown on GitHub while also rendering as a real
site with search, navigation, and native Mermaid diagrams.

## Decision

We will use MkDocs with the Material theme, deployed via GitHub Actions to
GitHub Pages, with `docs/` as the single source for both repository
browsing and the generated site.

### Alternatives considered

- **Jekyll** — rejected; no native Mermaid rendering (fragile JS-injection
  workarounds) and weak navigation/search without heavy plugin work.
- **Docusaurus** — rejected; its Node/React/MDX toolchain is heavier than a
  single-owner docs repo needs, and MDX diverges from plain markdown.
- **Raw markdown on GitHub, no generated site** — rejected; no real
  website, no reliable navigation or search.

## Consequences

- **Positive**: plain-markdown `docs/` stays readable directly on GitHub
  while still building a real site with navigation, search, and native
  Mermaid.
- **Negative**: adds a Python toolchain dependency the repository did not
  have before.
- **Follow-ups**: None.
