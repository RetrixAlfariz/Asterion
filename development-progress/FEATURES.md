# Asterion Development Progress — Feature Tree

> Branch-aware development tracker for Asterion.
>
> This file intentionally does **not** use completion percentages. A feature is tracked by its actual implementation state, current concrete changes, dependencies, and the branch where those changes live.

**Current development branch:** `v1`  
**Main branch policy:** `main` remains untouched until a coherent v1 foundation is ready to merge.  
**Last updated:** 2026-08-30

---

## Status Language

Use only descriptive states:

- `planned` — agreed direction, implementation has not started.
- `designing` — data model, interfaces, or architecture are actively being defined.
- `in-progress` — implementation exists and is actively changing.
- `implemented` — the intended feature exists on the listed branch.
- `validation` — implementation exists but is being tested against real material.
- `blocked` — progress depends on another unresolved component.
- `deferred` — intentionally postponed.
- `superseded` — replaced by a newer design or implementation.

For anything in `designing`, `in-progress`, or `validation`, always record **Current changes**. Do not replace this with a numeric percentage.

---

# Development Tree

```text
Asterion
└── v1
    ├── Foundation
    │   ├── Project identity                         [implemented]
    │   ├── Architecture contract                    [designing]
    │   ├── Canonical data model                     [designing]
    │   └── Local-first storage contract             [planned]
    │
    ├── Archive / Vault
    │   ├── Artifact registry                        [planned]
    │   ├── Content-addressed object storage         [planned]
    │   ├── Duplicate detection                      [planned]
    │   ├── Immutable source preservation            [planned]
    │   └── Document/version metadata                [planned]
    │
    ├── Document IR
    │   ├── Canonical block model                    [planned]
    │   ├── Page + hierarchy representation          [planned]
    │   ├── Stable SourceSpan locator                [planned]
    │   ├── Native PDF parser adapter                [planned]
    │   ├── Markdown parser adapter                  [planned]
    │   └── OCR / visual parser adapter contract     [deferred]
    │
    ├── Knowledge / Atlas
    │   ├── Concept model                            [planned]
    │   ├── Concept aliases                          [planned]
    │   ├── Claim model                              [planned]
    │   ├── Relation model                           [planned]
    │   ├── Evidence links                           [planned]
    │   ├── Candidate semantic state                 [planned]
    │   └── Semantic validation state                [planned]
    │
    ├── Curriculum Model
    │   ├── Institution                              [planned]
    │   ├── Program                                  [planned]
    │   ├── Academic term                            [planned]
    │   ├── Course                                   [planned]
    │   ├── Module/topic                             [planned]
    │   └── Course ↔ Concept mapping                 [planned]
    │
    ├── Retrieval / Navigator
    │   ├── SQLite FTS lexical retrieval             [planned]
    │   ├── Semantic retrieval                       [deferred]
    │   ├── Graph traversal                          [planned]
    │   ├── Source-aware ranking                     [planned]
    │   └── Context reconstruction                   [planned]
    │
    ├── Learner / Chronicle
    │   ├── LearnerEvent model                       [planned]
    │   ├── Exposure/review events                   [planned]
    │   ├── Problem-attempt events                   [planned]
    │   ├── Confidence events                        [planned]
    │   ├── Derived LearnerState                     [planned]
    │   └── Advanced knowledge tracing               [deferred]
    │
    ├── Reasoning
    │   ├── Source comparison                        [planned]
    │   ├── Relation traversal                       [planned]
    │   ├── Claim disagreement handling              [planned]
    │   ├── Cross-domain concept bridges             [planned]
    │   └── Provenance-aware answer verification     [planned]
    │
    ├── Asterion Interface
    │   ├── Search mode                              [planned]
    │   ├── Explain mode                             [planned]
    │   ├── Tutor mode                               [deferred]
    │   ├── Quiz mode                                [deferred]
    │   ├── Compare mode                             [deferred]
    │   ├── Explore mode                             [deferred]
    │   └── Study planning                           [deferred]
    │
    ├── Integrations
    │   ├── CorpusIndomicus bridge                   [planned]
    │   ├── Arline semantic idea adaptation          [planned]
    │   ├── Starforge academic-pattern reuse         [planned]
    │   ├── Ortheon document-vision adapter          [deferred]
    │   └── Arlume visual adapter                    [deferred]
    │
    └── First Vertical Slice
        └── One-book academic archive                [planned]
            ├── Register one source                  [planned]
            ├── Recover document hierarchy           [planned]
            ├── Preserve stable source spans         [planned]
            ├── Extract concepts + claims            [planned]
            ├── Link evidence                        [planned]
            ├── Search by concept                    [planned]
            └── Trace result to exact source         [planned]
```

---

# Active Branch State

## `v1`

**State:** `designing`

### Current changes

- Created the `v1` development branch while leaving `main` unchanged.
- Established the project README and Asterion's initial identity as a **Personal Academic Intelligence & Semantic Archive**.
- Defined the principles of local-first operation, source preservation, provenance, semantic structure, and separation between academic knowledge and learner state.
- Established the initial canonical knowledge concepts: `Document`, `Section`, `Concept`, `Claim`, `Relation`, `SourceSpan`, and `LearnerState`.
- Defined Asterion's intended relationship to Starforge, Arline, CorpusIndomicus, and the broader Parexa family.
- Established this tree-based development tracking model.

### Current design focus

The next architectural work is to turn the README-level concepts into explicit implementation contracts for:

1. Archive / artifact identity.
2. Asterion Document IR.
3. `SourceSpan` stability.
4. Concept / Claim / Relation / Evidence semantics.
5. SQLite-backed local persistence.
6. The first one-book vertical slice.

### Not started yet

- No ingestion code exists.
- No database schema is frozen.
- No semantic extraction pipeline exists.
- No learner-state logic exists.
- No conversational Asterion interface exists.

This is intentional. v1 is currently establishing the representation before adding model-driven behavior.

---

# Feature Records

## FND-001 — Project Identity

**State:** `implemented`  
**Branch:** `v1`

### Purpose

Define what Asterion is before implementation begins.

### Current changes

- Asterion is defined as a local-first personal academic intelligence and semantic archive.
- The archive is designed to remain useful without an LLM.
- Chat/tutoring are consumers of structured academic knowledge rather than the storage model itself.

### Next change

Move architecture details that become implementation contracts into dedicated files under `docs/` as the design stabilizes.

---

## FND-002 — Architecture Contract

**State:** `designing`  
**Branch:** `v1`

### Purpose

Separate Asterion into clear subsystems so parsing, semantics, learning state, retrieval, and assistant behavior do not collapse into one backend.

### Target structure

```text
Artifact Archive / Vault
        ↓
Document IR
        ↓
Knowledge / Atlas
        ↕
Curriculum Model
        ↕
Learner / Chronicle
        ↓
Retrieval + Reasoning
        ↓
Asterion Interface
```

### Current changes

- Identified Archive, Document IR, Knowledge, Curriculum, Learner, Retrieval, Reasoning, and Interface as separate responsibilities.
- Decided the assistant layer must consume the archive instead of owning it.
- Decided Arline should initially contribute design ideas rather than become a hard runtime dependency.

### Next change

Create `docs/ARCHITECTURE.md` once subsystem boundaries and data ownership are explicit enough to function as an implementation contract.

---

## ARC-001 — Source Artifact Registry

**State:** `planned`  
**Target branch:** `v1` or a future feature branch derived from `v1`

### Purpose

Register original files as stable artifacts before any parsing or semantic processing occurs.

### Intended behavior

```text
file
 ↓
hash
 ↓
artifact identity
 ↓
immutable source object
 ↓
document/version metadata
```

### Planned fields

- artifact ID
- content hash
- original filename
- MIME/source type
- file size
- ingestion timestamp
- storage locator
- source metadata

### Dependencies

- Local persistence contract.
- Object-storage layout.

---

## ARC-002 — Content-addressed Source Vault

**State:** `planned`  
**Target branch:** `v1` or archive feature branch

### Purpose

Preserve original academic artifacts immutably and deduplicate identical content.

### Intended behavior

- Hash file contents, likely using BLAKE3 or another explicitly selected hash.
- Store source objects by content identity.
- Detect repeated ingestion of the same artifact.
- Keep semantic transformations outside the original object.

### Dependency

`ARC-001`

---

## DIR-001 — Asterion Document IR

**State:** `planned`  
**Target branch:** document-IR feature branch derived from `v1`

### Purpose

Provide one canonical representation between document parsers and semantic extraction.

### Initial block vocabulary

```text
heading
paragraph
list
table
equation
figure
caption
code
quote
footnote
citation
page_header
page_footer
```

### Required properties

- block identity
- document identity
- page/location
- hierarchy
- text/content
- optional geometry/bounding box
- stable source linkage

### Dependencies

- Artifact/document identity.
- `SourceSpan` design.

---

## DIR-002 — Stable SourceSpan

**State:** `planned`  
**Target branch:** same branch as Document IR unless split later

### Purpose

Allow every supported semantic claim to be traced to an exact useful location in an original source.

### Locator types to support eventually

- page
- section
- paragraph/block
- line range
- slide
- timestamp
- bounding box

### Design requirement

A `SourceSpan` must survive ordinary re-indexing and semantic reprocessing. It should not depend solely on unstable chunk numbers.

---

## KNO-001 — Concept Model

**State:** `planned`  
**Target branch:** semantic-core feature branch derived from `v1`

### Purpose

Represent stable academic concepts independently of whichever documents mention them.

### Required direction

```text
Concept != source passage
Concept != claim
Concept != learner state
```

A concept may have multiple aliases and appear in many courses and documents.

---

## KNO-002 — Claim + Evidence Model

**State:** `planned`  
**Target branch:** semantic-core feature branch

### Purpose

Represent what individual sources actually assert, with explicit evidence.

### Intended structure

```text
Concept
   ↑
 Claim
   ↓
Evidence
   ↓
SourceSpan
```

### Design rule

Asterion must be able to preserve conflicting claims from different sources rather than silently collapsing them into one generated definition.

### Dependency

- `DIR-002`
- `KNO-001`

---

## KNO-003 — Relation Model

**State:** `planned`  
**Target branch:** semantic-core feature branch

### Purpose

Represent explicit semantic relationships between concepts, claims, documents, courses, and external entities.

### Initial relation vocabulary

- prerequisite_of
- derived_from
- applied_in
- related_to
- equivalent_to
- contradicts
- extends
- example_of
- part_of
- discussed_in
- regulated_by
- superseded_by

Relations should carry evidence or derivation metadata where applicable.

---

## KNO-004 — Candidate Semantics

**State:** `planned`  
**Target branch:** semantic-core feature branch

### Purpose

Prevent model extraction from becoming database truth merely because a model produced it confidently.

### Intended states

```text
candidate
accepted
rejected
superseded
```

### Required metadata

- extraction method/model
- extraction run
- confidence signal
- evidence span
- validation state

---

## CUR-001 — Curriculum Model

**State:** `planned`  
**Target branch:** `v1` or dedicated curriculum feature branch

### Purpose

Explain why knowledge exists in the archive and where it belongs academically.

### Target hierarchy

```text
Institution
 ↓
Program
 ↓
Academic Term
 ↓
Course
 ↓
Module / Topic
 ↓
Concept
```

This must support multiple institutions and disciplines in one Asterion archive.

---

## RET-001 — Local Retrieval Foundation

**State:** `planned`  
**Target branch:** retrieval feature branch

### Initial scope

Start with explainable retrieval:

- SQLite FTS5 lexical search.
- exact concept/alias lookup.
- graph traversal.
- source-aware ranking.
- source-context reconstruction.

Vector retrieval should be added after the structured path works rather than being treated as the entire retrieval architecture.

### Dependencies

- Document IR.
- Concept/Claim model.
- Local persistence.

---

## LRN-001 — LearnerEvent Log

**State:** `planned`  
**Target branch:** learner feature branch

### Purpose

Track observable learning interactions before deriving mastery values.

### Initial events

- concept exposure
- review
- explanation requested
- problem attempt
- correct/incorrect result
- quiz completion
- self-reported confidence
- delayed revisit

### Design rule

Asking about a concept does not imply mastery of the concept.

---

## LRN-002 — Derived LearnerState

**State:** `planned`  
**Target branch:** learner feature branch

### Purpose

Summarize the user's current relationship to a concept from the event history.

### Initial state should remain interpretable

- exposure count
- attempt count
- successful attempts
- last seen
- last success
- self-confidence
- observed weaknesses
- qualitative learning status

Advanced knowledge-tracing models remain deferred until real learning-event data exists.

### Dependency

`LRN-001`

---

## INT-001 — CorpusIndomicus Bridge

**State:** `planned`  
**Target branch:** future integration branch derived from a stable v1 semantic core

### Purpose

Connect academic legal material to authoritative Indonesian legal sources without treating them as the same source class.

### Intended capability

```text
academic claim
      ↓ compare
legal authoritative claim
      ↓
version / amendment awareness
      ↓
discrepancy surfaced to user
```

### Dependencies

- Claim/evidence model.
- Source authority model.
- Stable CorpusIndomicus interface.

---

## VSL-001 — First Vertical Slice: One Academic Book

**State:** `planned`  
**Target branch:** `v1` after the minimum architecture contracts are ready

### Purpose

Prove Asterion with one real academic source before expanding the system horizontally.

### Target flow

```text
one book
  ↓
artifact registration
  ↓
document hierarchy
  ↓
source spans
  ↓
concepts + claims
  ↓
evidence links
  ↓
concept search
  ↓
exact source trace
```

### Success condition

A user can search for a concept and Asterion can return the relevant structured academic material while tracing every surfaced claim back to a useful location in the original source.

### Explicitly outside this slice

- full tutor behavior
- study planner
- advanced learner mastery models
- broad cross-domain reasoning
- visual engineering-document understanding
- distributed services

---

# Branch Tracking Convention

When a feature moves to a dedicated branch, record it directly under the feature instead of describing progress generically.

Example:

```markdown
## DIR-001 — Asterion Document IR

**State:** `in-progress`
**Branch:** `feature/document-ir`

### Current changes

- Added base `DocumentBlock` schema.
- Added heading and paragraph block variants.
- Added page locators.
- SourceSpan identity is still unresolved for tables split across pages.

### Next change

- Add table/figure block variants.
- Freeze span identity rules.
```

The development tree should then reflect that branch:

```text
v1
└── feature/document-ir
    ├── block schema                  [implemented]
    ├── page locators                 [implemented]
    ├── table blocks                  [in-progress]
    └── stable SourceSpan identity    [designing]
```

This makes the tracker describe **what actually exists on each branch**, not an arbitrary estimate of how complete someone feels the feature is.

---

# Update Rules

When development changes:

1. Update the relevant feature's `State`.
2. Record the exact branch containing the change.
3. Replace or append **Current changes** with concrete implementation facts.
4. Record unresolved design questions under that feature rather than hiding them behind a percentage.
5. Add newly discovered child features to the tree instead of silently expanding the scope of an existing node.
6. Mark abandoned designs as `superseded` or `deferred`; do not erase useful development history.
7. Do not mark a parent feature `implemented` while required child nodes are still `planned` unless the parent explicitly represents only a narrower contract.
8. Keep `main` out of this tracker until work is intentionally merged there.

---

# Immediate Next Nodes

The next development sequence should currently be:

```text
FND-002 Architecture Contract
        ↓
ARC-001 Artifact Registry
        +
DIR-001 Document IR
        +
DIR-002 SourceSpan
        ↓
KNO-001 Concept Model
        +
KNO-002 Claim + Evidence
        +
KNO-003 Relations
        ↓
VSL-001 One-book Vertical Slice
```

Everything else can grow from evidence gathered while building that path.
