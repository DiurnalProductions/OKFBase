# Contributing to OKFBase

OKFBase is the public catalog of [OKF](https://github.com/DiurnalProductions/OKFBase) (Open Knowledge Format) bundles used by [Boone](https://github.com/DiurnalProductions/Boone), the mobile knowledge-base viewer. This repository does not host knowledge content itself — it lists links to standalone knowledge-pack repositories that Boone can download and install.

To add a new topic to the catalog, you create a separate GitHub repository for the pack, then open a pull request here that adds an entry to `catalog.json`.

## Overview

```
your-topic-okf/          ← standalone GitHub repo (the knowledge pack)
  okf.json               ← pack metadata
  index.md               ← landing page / learning paths
  concepts/              ← one markdown file per concept
  README.md
  LICENSE

OKFBase/
  catalog.json           ← registry Boone reads to discover packs
```

Boone fetches `catalog.json` from the `main` branch of this repository, then downloads each listed pack from its GitHub source.

## OKF knowledge package prompt template

The sections below describe the pack format manually. If you are generating a new pack with an LLM, use the **generic prompt template** in this section: copy it, replace the `{{PLACEHOLDER}}` values, and run it. The output should be a complete repository you can push to GitHub and list in `catalog.json` (Steps 2–3 below).

The prompt targets [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) — a directory of markdown concept files with YAML frontmatter, linked by ID-based references. That matches what Boone expects.

### The prompt

````markdown
# ✔ GENERIC PROMPT: OKF KNOWLEDGE PACKAGE REPO

## BUILD AN OKF v0.1 KNOWLEDGE PACKAGE REPOSITORY

You are generating a **complete OKF-compliant knowledge repository** for:

> **{{PACK_NAME}}**

This is NOT an application, NOT a runtime system, and NOT a codebase implementation.

This output is a **self-contained OKF knowledge pack repository** that can be installed into an OKF-compatible knowledge system.

---

# CONTEXT

This repository must follow:

Open Knowledge Format v0.1 specification:
https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md

OKF is:

* A directory of markdown files
* Each file represents a knowledge concept node
* Each file uses YAML frontmatter metadata
* Relationships between concepts are expressed via ID-based references

---

# OUTPUT REQUIREMENT

Generate a full repository:

```text
{{PACK_ID}}-okf/
  okf.json (manifest)
  index.md
  /concepts
    {{CONCEPT_FILES}}
```

Where `{{CONCEPT_FILES}}` is a structured set of markdown files derived from:

{{TOPIC_LIST}}

---

# 1. okf.json (REQUIRED MANIFEST)

Create a root manifest:

```json
{
  "id": "{{PACK_ID}}-okf",
  "name": "{{PACK_NAME}} Knowledge Pack",
  "version": "0.1",
  "description": "{{PACK_DESCRIPTION}}",
  "entry": "index.md",
  "tags": [{{PACK_TAGS}}]
}
```

---

# 2. OKF FILE FORMAT (STRICT)

Every `.md` file MUST follow OKF v0.1 specification:

```yaml
---
id: {{CONCEPT_ID}}
type: concept
title: {{CONCEPT_TITLE}}
description: {{CONCEPT_DESCRIPTION}}
tags: [{{CONCEPT_TAGS}}]
prerequisites:
  - {{PREREQUISITE_IDS}}
related:
  - {{RELATED_IDS}}
resource: {{OPTIONAL_RESOURCE_URL}}
timestamp: 2026-01-01
---
```

Followed by markdown content.

---

# 3. CONTENT REQUIREMENTS

Each concept file must include:

## Required sections:

* Summary
* Mental model (CRITICAL: explain core intuition clearly)
* Example usage (conceptual, not full project code unless appropriate)
* Common mistakes
* Related concepts

Keep content concise, structured, and optimized for learning systems.

---

# 4. REQUIRED CONCEPT GRAPH RULES

You MUST construct a coherent dependency graph:

* Concepts must form a **directed acyclic graph (DAG)**
* All `prerequisites` must reference valid concept IDs
* No circular dependencies
* Relationships must reflect real learning progression, not file order

---

# 5. LEARNING STRUCTURE REQUIREMENTS

Organize concepts into:

### Foundational layer

{{FOUNDATIONAL_CONCEPTS}}

### Core system layer

{{CORE_CONCEPTS}}

### Advanced layer

{{ADVANCED_CONCEPTS}}

### Cross-links

* Add meaningful `related` links between non-dependent but conceptually connected nodes

---

# 6. index.md (REQUIRED)

Create a structured entry point:

Must include:

* "Start here" section
* recommended learning path
* grouped concept hierarchy
* progression order from beginner → advanced

---

# 7. OKF COMPLIANCE RULES

You must strictly enforce:

* Every file has: `id`, `type`, `title`
* All relationships use ID references only
* No missing prerequisite references
* No circular dependencies
* No duplicate IDs
* No application code or runtime logic
* This is documentation-only knowledge

---

# 8. INTENT OF THIS PACKAGE

This repository is meant to be:

> A plug-and-play OKF knowledge pack for **{{PACK_NAME}}**

It behaves like:

* an npm package for knowledge
* a graph-based learning module
* a composable node in a larger knowledge ecosystem

---

# 9. OUTPUT FORMAT

Return ONLY:

* full folder structure
* full file contents

Do NOT include:

* explanations
* commentary
* extra text
* meta discussion

---

## END
````

### Placeholder reference

Replace every `{{PLACEHOLDER}}` before running the prompt.

#### Core identity

| Placeholder | Example | What it controls |
|-------------|---------|------------------|
| `{{PACK_ID}}` | `react`, `css`, `nextjs` | Short slug for the topic. Combined with `-okf` to form the repo name and `okf.json` → `id` (e.g. `react-okf`). Must match [repository naming](#repository-naming) rules. |
| `{{PACK_NAME}}` | `React`, `CSS`, `Next.js` | Human-readable topic name used in titles and descriptions. |
| `{{PACK_DESCRIPTION}}` | `OKF knowledge base for React fundamentals` | One-line summary for `okf.json` and the Boone catalog. |
| `{{PACK_TAGS}}` | `"react", "frontend", "javascript", "ui"` | Comma-separated JSON string values for catalog search/filter tags. |

#### Structure and content

| Placeholder | Example | What it controls |
|-------------|---------|------------------|
| `{{TOPIC_LIST}}` | Bulleted list of every concept to cover | The authoritative scope of the pack. The model derives filenames, frontmatter, and graph edges from this list. |
| `{{CONCEPT_FILES}}` | `components.md`, `hooks.md`, `state.md` | Filenames under `concepts/`. Usually snake_case or kebab-case (see [file naming](#file-naming)). One file per major concept. |

#### Graph shaping

| Placeholder | What it controls |
|-------------|------------------|
| `{{FOUNDATIONAL_CONCEPTS}}` | Entry-level ideas with few or no prerequisites — the "start here" layer. |
| `{{CORE_CONCEPTS}}` | Central topic mechanics learners need after fundamentals. |
| `{{ADVANCED_CONCEPTS}}` | Specialized or edge-case topics that depend on core concepts. |

These three lists guide how `prerequisites` and `related` edges are assigned. They do not create separate directories — all concepts still live in `concepts/`.

#### Per-concept placeholders (in the frontmatter template)

| Placeholder | Example | What it controls |
|-------------|---------|------------------|
| `{{CONCEPT_ID}}` | `react.components` | Stable graph identifier used in `prerequisites` and `related`. Convention: `{pack-prefix}.{slug}` where the prefix is usually `{{PACK_ID}}`. |
| `{{CONCEPT_TITLE}}` | `Components` | Display title in Boone and in `index.md` links. |
| `{{CONCEPT_DESCRIPTION}}` | Short summary sentence | Shown in search results and concept lists. |
| `{{CONCEPT_TAGS}}` | `react, ui, components` | Keywords for the individual concept. |
| `{{PREREQUISITE_IDS}}` | `react.jsx` | Concept IDs that must be understood first. Use `[]` for foundational topics. |
| `{{RELATED_IDS}}` | `react.props`, `react.state` | Non-dependent but related concepts (cross-links). |
| `{{OPTIONAL_RESOURCE_URL}}` | `https://react.dev/...` | External reference link. Omit the `resource:` line if none applies. |

### What each prompt section means

**BUILD / CONTEXT** — Sets scope: output is a documentation-only knowledge pack, not an app. Points the model at the [OKF v0.1 spec](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). Same intent as the [overview](#overview) above.

**OUTPUT REQUIREMENT** — Defines the repo tree the model must produce. Matches the layout in [Step 1](#step-1--create-a-knowledge-pack-repository). `{{CONCEPT_FILES}}` is filled implicitly when you provide `{{TOPIC_LIST}}`.

**§1 okf.json** — Root manifest. Field-by-field details are in [`okf.json`](#okfjson). The `id` must equal `{{PACK_ID}}-okf`.

**§2 OKF file format** — YAML frontmatter schema for every concept file. See [frontmatter](#frontmatter) for field notes. Boone also requires `type` and `title`; the prompt treats frontmatter `id` as required for graph integrity.

**§3 Content requirements** — Required body sections for each concept. Matches [body sections](#body-sections). "Mental model" is emphasized because it is the hardest section for models to write well — it should capture intuition, not just definitions.

**§4 Concept graph rules** — The `prerequisites` field must form a **DAG** (no cycles). Every ID in `prerequisites` or `related` must exist on some concept in the pack. Boone uses these edges for learning-path ordering and cross-pack analysis.

**§5 Learning structure** — Tells the model how to layer concepts before writing files. Foundational → core → advanced is reflected in both `prerequisites` depth and the grouped sections in `index.md`.

**§6 index.md** — Entry page requirements. See [`index.md`](#indexmd). Link to concepts with root-absolute paths like `/concepts/components.md`.

**§7 Compliance rules** — Hard constraints to validate before publishing. Overlaps with the [pull request checklist](#pull-request-checklist). "Documentation-only" means no deployable app code in the repo.

**§8 Intent** — Frames the deliverable as an installable knowledge module (like an npm package for facts), consistent with how Boone treats catalog entries.

**§9 Output format** — Tells the model to return raw files only, with no preamble — so you can copy the output directly into a new repository.

### Example instance: TypeScript (`typescript-okf`)

The prompt below is the **filled-in version** of the [generic template](#the-prompt) used to generate [typescript-okf](https://github.com/DiurnalProductions/typescript-okf). Compare it to the generic prompt to see how placeholders become concrete requirements:

| Generic placeholder | TypeScript value |
|---------------------|------------------|
| `{{PACK_ID}}` | `typescript` |
| `{{PACK_NAME}}` | `TypeScript` |
| `{{PACK_DESCRIPTION}}` | Type system, inference, generics, structural typing, and JS interop |
| `{{PACK_TAGS}}` | `"typescript", "ts", "javascript", "typing", "programming"` |
| `{{TOPIC_LIST}}` / `{{CONCEPT_FILES}}` | Replaced by **§2 REQUIRED CONCEPT SCOPE** — a mandatory category checklist the model must cover |
| `{{FOUNDATIONAL_CONCEPTS}}` | Core type system fundamentals + structural typing model |
| `{{CORE_CONCEPTS}}` | Functions & generics, object modeling, module typing |
| `{{ADVANCED_CONCEPTS}}` | Advanced types, advanced type system, runtime interop layer |

TypeScript goes further than the generic template in two ways:

- **§2 REQUIRED CONCEPT SCOPE** — an explicit, non-optional topic checklist instead of a free-form `{{TOPIC_LIST}}`. Use this pattern when a pack must cover a known curriculum.
- **§5 / §7** — topic-specific graph progressions and quality themes (compile-time vs runtime, structural typing, control-flow narrowing) that replace the generic learning-structure and compliance sections.

Concept IDs use the `typescript.{slug}` prefix throughout (e.g. `typescript.basic_types`).

#### The prompt

````markdown
## BUILD AN OKF v0.1 TYPESCRIPT KNOWLEDGE PACKAGE REPOSITORY

You are generating a **complete OKF-compliant knowledge repository for modern TypeScript (strict mode, type system design, and JavaScript interop model)**.

This is NOT an application, NOT a runtime system, and NOT a code project.

This output is a **self-contained OKF knowledge pack repository** that can be installed into an OKF-compatible knowledge system.

---

# CONTEXT

This repository must follow:

Open Knowledge Format v0.1 specification:
https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md

OKF is:

* A directory of markdown files
* Each file represents a concept node
* Each file uses YAML frontmatter metadata
* Relationships are expressed via ID-based references

---

# OUTPUT REQUIREMENT

Generate a full repository:

```text
typescript-okf/
  okf.json (manifest)
  index.md
  /concepts
```

You must design all concept files yourself based on modern TypeScript fundamentals.

---

# 1. okf.json (REQUIRED MANIFEST)

Create a root manifest:

```json
{
  "id": "typescript-okf",
  "name": "TypeScript Knowledge Pack",
  "version": "0.1",
  "description": "OKF knowledge base for TypeScript (type system, inference, generics, structural typing, and JS interop)",
  "entry": "index.md",
  "tags": ["typescript", "ts", "javascript", "typing", "programming"]
}
```

---

# 2. REQUIRED CONCEPT SCOPE

You MUST include concepts covering:

### Core type system fundamentals

* basic types
* type annotations
* type inference
* any / unknown / never

### Structural typing model

* type compatibility
* duck typing in TS
* excess property checks

### Advanced types

* unions
* intersections
* literal types
* type narrowing

### Functions & generics

* generics
* constraints
* default generics
* function typing

### Object modeling

* interfaces
* type aliases
* index signatures

### Runtime interop with JS

* structural mismatch risks
* erasure model
* JS runtime vs TS compile time

### Module typing system

* module augmentation
* declaration merging
* ambient types

### Advanced type system

* mapped types
* conditional types
* infer keyword
* utility types

You may add additional concepts if needed, but must not omit any category above.

---

# 3. OKF FILE FORMAT (STRICT)

Every `.md` file MUST follow OKF v0.1 specification:

```yaml
---
id: typescript.concept_name
type: concept
title: Human-readable concept title
description: Clear explanation of the TypeScript concept
tags: [typescript, relevant, tags]
prerequisites:
  - typescript.other_concept
related:
  - typescript.other_concept
resource: https://www.typescriptlang.org/docs/
timestamp: 2026-01-01
---
```

Followed by markdown content.

---

# 4. CONTENT REQUIREMENTS

Each concept file must include:

## Required sections:

* Summary
* Mental model (CRITICAL: explain compile-time vs runtime separation clearly)
* Example (small TypeScript snippets allowed)
* Common mistakes (especially inference pitfalls and narrowing errors)
* Related concepts

Content must be:

* accurate for modern TypeScript (strict mode assumptions)
* focused on type system behavior, not just syntax
* explicit about what disappears at runtime

---

# 5. REQUIRED DEPENDENCY GRAPH RULES

You must construct a coherent learning graph:

### Core progression:

* basic types → type inference → unions → narrowing → functions → generics

### Structural system:

* interfaces → type aliases → structural typing → compatibility

### Advanced system:

* generics → constraints → conditional types → mapped types → infer

### Runtime relationship layer:

* JavaScript interop → type erasure → runtime vs compile-time separation

Ensure:

* graph is a **directed acyclic graph (DAG)**
* no circular dependencies
* prerequisites reflect conceptual dependency, not file ordering

---

# 6. index.md (REQUIRED)

Create a structured entry point.

Must include:

* "Start here" section
* recommended learning progression
* grouping into:

  * fundamentals
  * type modeling
  * generics system
  * advanced type mechanics
  * JS interop model
* explanation of why TypeScript is "compile-time only"

---

# 7. CONTENT QUALITY REQUIREMENTS

Each concept must emphasize:

* TypeScript types do not exist at runtime
* structural typing is the core compatibility model
* inference is context-sensitive, not static rules
* generics are compile-time functions over types
* narrowing is control-flow based reasoning

---

# 8. OKF COMPLIANCE RULES

You must strictly enforce:

* Every file has: `id`, `type`, `title`
* All relationships use ID references only
* No missing prerequisite references
* No circular dependencies
* No duplicate IDs
* No runtime application code
* This is documentation-only knowledge

---

# 9. INTENT OF THIS PACKAGE

This repository is meant to be:

> A plug-and-play OKF knowledge pack for TypeScript that can be installed into an OKF viewer system.

It behaves like:

* an npm package for knowledge
* a graph-based learning module
* a composable node in a larger knowledge ecosystem

---

# 10. OUTPUT FORMAT

Return ONLY:

* full folder structure
* full file contents

Do NOT include:

* explanations
* commentary
* extra text
* meta discussion

---

## END
````

The live output of this prompt is [typescript-okf](https://github.com/DiurnalProductions/typescript-okf) — use it as a reference implementation alongside the [other examples](#reference-implementations).

### After generation

1. Review the output against the [pull request checklist](#pull-request-checklist).
2. Add `LICENSE` and `README.md` if the model did not include them.
3. Push to a public `{{PACK_ID}}-okf` repository ([Step 2](#step-2--publish-the-repository)).
4. Open a PR here to add the pack to `catalog.json` ([Step 3](#step-3--add-the-pack-to-catalogjson)).

## Step 1 — Create a knowledge-pack repository

### Repository naming

Use the pattern **`{topic}-okf`**, all lowercase, with words separated by hyphens:

| Topic | Repository name | Pack `id` |
|-------|-----------------|-----------|
| TypeScript | `typescript-okf` | `typescript-okf` |
| React Native | `react-native-okf` | `react-native-okf` |
| Python | `python-okf` | `python-okf` |

The pack `id` in `okf.json` **must exactly match** the repository name and the `id` you will use in `catalog.json`. Boone rejects packs where these do not align.

### Required files

Every knowledge-pack repository must contain at minimum:

| File | Purpose |
|------|---------|
| `okf.json` | Pack metadata (required by Boone) |
| `index.md` | Entry point with overview and recommended learning paths |
| `concepts/*.md` | Individual concept documents |
| `LICENSE` | License for the pack content |
| `README.md` | Short description of the repository |

Place all concept files under a `concepts/` directory at the repository root. Boone scans the repository for `.md` files and loads every markdown file except reserved names (see below).

### `okf.json`

```json
{
  "id": "typescript-okf",
  "name": "TypeScript Knowledge Pack",
  "version": "0.1",
  "description": "OKF knowledge base for TypeScript (type system, inference, generics, structural typing, and JS interop)",
  "entry": "index.md",
  "tags": ["typescript", "ts", "javascript", "typing", "programming"]
}
```

| Field | Required | Notes |
|-------|----------|-------|
| `id` | Yes | Must match the repo name and catalog entry `id` |
| `name` | Yes | Human-readable display name |
| `version` | Yes | Semantic version string (e.g. `"0.1"`) |
| `description` | Yes | One-line summary shown in the Boone catalog |
| `entry` | Yes | Landing page filename, typically `"index.md"` |
| `tags` | Recommended | Search and filter keywords |

### `index.md`

The index is the pack's table of contents. It should:

- Introduce the topic and who it is for
- Link to a recommended starting concept
- Provide one or more learning paths as ordered lists of links

Use root-absolute paths for concept links:

```markdown
## Start here

Begin with [Basic Types](/concepts/basic_types.md).

## Recommended path

1. [Basic Types](/concepts/basic_types.md)
2. [Type Annotations](/concepts/type_annotations.md)
3. [Type Inference](/concepts/type_inference.md)
```

`index.md` is not loaded as a concept — it is only the entry page.

### Concept files (`concepts/*.md`)

Each concept is a markdown file with a YAML frontmatter block followed by a body.

#### Frontmatter

```yaml
---
id: typescript.basic_types
type: concept
title: Basic Types
description: Primitive and foundational types in TypeScript's static type system under strict mode.
tags: [typescript, fundamentals, types, primitives]
prerequisites: []
related:
  - typescript.type_annotations
  - typescript.type_inference
resource: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html
timestamp: 2026-01-01
---
```

| Field | Required | Notes |
|-------|----------|-------|
| `type` | Yes | Use `concept` for standard topics |
| `title` | Yes | Display title |
| `description` | Yes | Short summary |
| `id` | Yes | Stable graph identifier (`{pack-prefix}.{slug}`). **Required** when following the [prompt template](#okf-knowledge-package-prompt-template); used in `prerequisites` and `related` |
| `tags` | Recommended | Topic keywords |
| `prerequisites` | Recommended | List of concept `id` values that should be understood first |
| `related` | Recommended | List of related concept `id` values |
| `resource` | Optional | External reference URL |
| `timestamp` | Optional | ISO date the concept was written or last reviewed |

#### Body sections

Follow a consistent structure across concepts in a pack. Existing packs typically include:

- **Summary** — concise explanation of the concept
- **Mental model** — how to think about it (**required** in the [prompt template](#okf-knowledge-package-prompt-template); encouraged for all packs)
- **Example** — code or concrete illustration
- **Common mistakes** — pitfalls learners encounter
- **Related concepts** — markdown links to other concepts in the pack

#### Linking between concepts

Link to other concepts using root-absolute paths:

```markdown
See [Type Annotations](/concepts/type_annotations.md) for syntax details.
```

Boone resolves these links to build the in-pack concept graph. External URLs (`https://…`) are stored as resources but are not treated as in-pack links.

#### Reserved filenames

These files are **not** loaded as concepts:

- `index.md`
- `log.md`

Do not place concept content in files with these names.

### File naming

Use lowercase filenames with underscores or hyphens:

```
concepts/basic_types.md
concepts/type_annotations.md
concepts/semantic-html.md
```

The concept id Boone derives from the file path is the path without `.md` (e.g. `concepts/basic_types`).

## Step 2 — Publish the repository

1. Push the pack to a **public** GitHub repository under the `{topic}-okf` name.
2. Ensure `okf.json` and all concept files are on the `main` branch (or whichever branch you will reference in the catalog).
3. Verify the repository is accessible without authentication — Boone downloads files via the public GitHub raw content API.

## Step 3 — Add the pack to `catalog.json`

Open a pull request against this repository that adds an entry to the `packs` array in `catalog.json`:

```json
{
  "id": "typescript-okf",
  "name": "TypeScript Knowledge Pack",
  "description": "OKF knowledge base for TypeScript (type system, inference, generics, structural typing, and JS interop)",
  "version": "0.1",
  "tags": ["typescript", "ts", "javascript", "typing", "programming"],
  "source": {
    "type": "github",
    "owner": "DiurnalProductions",
    "repo": "typescript-okf",
    "ref": "main"
  }
}
```

| Field | Notes |
|-------|-------|
| `id` | Must match `okf.json` → `id` and the repository name |
| `name` | Display name (usually matches `okf.json` → `name`) |
| `description` | Usually matches `okf.json` → `description` |
| `version` | Usually matches `okf.json` → `version` |
| `tags` | Usually matches `okf.json` → `tags` |
| `source.type` | Must be `"github"` |
| `source.owner` | GitHub organization or user |
| `source.repo` | Repository name (without owner) |
| `source.ref` | Branch, tag, or commit SHA (typically `"main"`) |
| `source.path` | Optional subdirectory if the pack files are not at the repo root |

Keep entries in **alphabetical order** by `id`.

### Updating an existing pack

Changes to concept content are made in the pack's own repository — no OKFBase PR is needed unless metadata in `catalog.json` should change (description, version bump, new tags, etc.).

## Pull request checklist

Before submitting a catalog PR, confirm:

- [ ] The pack repository is public and named `{topic}-okf`
- [ ] `okf.json` exists at the repo root with a valid `id` field
- [ ] `index.md` links to concepts using `/concepts/…` paths
- [ ] Every concept file has YAML frontmatter with at least `type`, `title`, and `description`
- [ ] `catalog.json` entry `id` matches `okf.json` → `id` and the repo name
- [ ] `source.repo` and `source.ref` point to the correct repository and branch
- [ ] The new entry is placed in alphabetical order in the `packs` array

## Reference implementations

These repositories follow the expected format and are good templates:

- [typescript-okf](https://github.com/DiurnalProductions/typescript-okf)
- [html-okf](https://github.com/DiurnalProductions/html-okf)
- [python-okf](https://github.com/DiurnalProductions/python-okf)

## Questions

Open an issue in this repository or the relevant pack repository if something is unclear about the format or catalog process.
