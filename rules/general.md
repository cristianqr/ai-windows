---
description: Language-agnostic code style conventions that apply to any codebase
alwaysApply: true
---

# General

## Naming

- **Semantic naming over structural or vague naming** — name variables/parameters/functions after what the data or operation *means* in the domain, not the data structure it sits in or a shorter approximation that loses meaning. Prefer `CARRIER_SHIPPING_RATES` over generic `ITEMS`/`DATA`/`ROWS`.
- When a variable is read from (or mirrors) a specific property/field, prefer a name that reflects that property rather than a vaguer paraphrase — e.g. `currentPickedQuantity`, not `currentQuantity`, when re-reading a `CompletedPickQuantity` field. If the reader has to re-derive what a name means from surrounding context, it's too vague.
- When an object's property name is fixed by an external contract (e.g. a UI library's `children` field), keep the required property name on the object but name the *local variable* for what it contains: `const cptSlotNodes = ...; return { ..., children: cptSlotNodes };`, not `const children = ...; return { ..., children };`.
