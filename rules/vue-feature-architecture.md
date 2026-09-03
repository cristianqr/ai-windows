---
description: Vue feature-module architecture conventions for MFE repos
globs: "**/*.vue"
alwaysApply: false
---

# Vue Feature-Module Architecture

Applies when editing Vue components and their associated services/logic modules in MFE repos
structured around feature modules (e.g. `src/features/<feature>/`).

## Structure

1. **Feature module boundaries** — respect the `pages/ containers/ components/ services/ logic/
   store/` split:
   - `pages/` — route-level views (one per route)
   - `containers/` — stateful "smart" components: fetch data, manage local state
   - `components/` — presentational components: props/emits only, no service calls
   - `services/` — API service class + `ServiceFactory` + mocks
   - `logic/` — pure business logic shared across containers, framework-agnostic (no Vue/Vuex
     imports); accept `$t` as a parameter if i18n is needed, don't import the i18n instance
     directly
   - `store/` — Vuex module, only when state must be shared across containers within the feature
2. **Service boundary casing** — components always work in camelCase. Key-case conversion
   (snake_case ↔ camelCase, e.g. via `keysToSnakeCase`/`keysToCamel` helpers) happens only inside
   the service layer — never in a component or a `logic/` module.

## Data & platform

3. **Structural data, not encoded strings** — don't encode meaning into an id/key string for later
   parsing (e.g. `` `${a}-slot-${b}` `` then `.split('-slot-')`). Carry the meaning as explicit
   fields on the data object and read those directly.
4. **Defensive platform access** — MFEs may run standalone, embedded in a native WebView shell, or
   in Electron. Don't assume any single host's platform APIs are present — feature-detect or guard.

## Accessibility

Follow `rules/frontend-accessibility.md` for markup accessibility MUSTs.
