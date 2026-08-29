# Asterion

> **Personal Academic Intelligence & Semantic Archive**

Asterion is a local-first system for turning scattered educational material into a traceable, structured model of a person's academic world.

It is not intended to be another generic "upload a PDF and chat with it" application. Asterion is designed to preserve the original source, understand how concepts relate to one another, track how the user relates to those concepts, and provide a persistent academic intelligence layer over that knowledge.

The project begins as a personal academic archive, but its architecture is intentionally general enough to support multiple disciplines, institutions, document types, and authoritative external corpora.

> **Status:** early design / v1 foundation. The architecture described here is the target direction, not a claim that every subsystem is already implemented.

---

## Why Asterion Exists

Academic material tends to become fragmented very quickly:

- textbooks live in PDFs or physical books,
- lecture slides sit in separate folders,
- notes are written elsewhere,
- assignments encode useful examples but disappear after submission,
- courses reuse concepts without explicitly connecting them,
- personal understanding changes over time,
- and authoritative sources may change after a textbook has already been published.

Traditional search treats these as files.

Traditional RAG often treats them as chunks.

Asterion instead aims to treat them as **knowledge with structure, provenance, relationships, and personal learning state**.

The core idea is simple:

```text
raw educational material
        ↓
source-preserving archive
        ↓
document structure
        ↓
concepts + claims + relations
        ↓
semantic academic graph
        ↓
user learning state
        ↓
search / tutor / planner / reasoning
```

The archive should remain useful even if every language model is removed from the system. AI is an interface and reasoning layer over trustworthy academic data, not a replacement for the data itself.

---

## Project Identity

**Asterion** is both the name of the project and the academic intelligence that operates over the archive.

Asterion should eventually be able to understand not only:

> "What is an eigenvalue?"

but also:

> "Where did I encounter eigenvalues, which courses depend on them, what sources explain them, and which related concepts am I still weak at?"

That distinction is central to the project.

A general academic assistant models knowledge.

Asterion additionally models **the user's relationship to knowledge**.

---

## Core Principles

### 1. Local-first

The academic archive should be usable locally and should not require sending the user's entire educational history to a remote service.

Remote models or services may be supported later, but the archive, indexes, user model, and core metadata should remain locally controllable.

### 2. Preserve the source

Asterion must not destroy or silently replace original material during ingestion.

Every transformation should be traceable back to the source artifact.

```text
Concept
  ↓ supported_by
Claim
  ↓ extracted_from
SourceSpan
  ↓ belongs_to
Section
  ↓ belongs_to
Document
  ↓ references
Original Artifact
```

### 3. Provenance before confidence

A confident answer without a traceable source is still just a confident rumor.

Asterion should distinguish between:

- directly sourced claims,
- derived semantic relations,
- model-generated interpretations,
- user-created notes,
- and authoritative external material.

### 4. Knowledge state is not user state

One of the most important architectural rules:

```text
WORLD / ACADEMIC MODEL != USER MODEL
```

If the user does not understand a concept, that does not make the concept itself uncertain.

Likewise, strong source confidence does not imply strong user mastery.

Asterion therefore keeps objective academic knowledge and personal learner state separate.

### 5. Structure before chat

The first success criterion is not "the chatbot gives impressive answers."

It is:

> **Can Asterion represent a concept and trace exactly where it came from?**

Chat, tutoring, study planning, and automated reasoning are consumers of the archive, not the archive itself.

### 6. Semantic relationships matter

Academic knowledge is not a pile of isolated definitions.

Asterion should represent relationships such as:

- prerequisite of,
- derived from,
- applied in,
- contradicts,
- extends,
- equivalent to,
- example of,
- part of,
- regulated by,
- superseded by,
- discussed in,
- and related to.

This allows the system to reason across courses rather than only within documents.

---

## Canonical Knowledge Model

The current canonical object chain is:

```text
Document
   ↓
Section
   ↓
Concept
   ↓
Claim
   ↓
Relation
   ↓
SourceSpan
```

A separate learner layer attaches personal state:

```text
Concept
   ↓
LearnerState
```

### Document

Represents an academic source artifact or logical document.

Examples:

- textbook,
- lecture slide deck,
- handwritten or typed notes,
- assignment,
- syllabus,
- paper,
- laboratory manual,
- legal instrument,
- web archive,
- or generated study material.

Expected metadata may include:

```yaml
title: Ilmu Negara
institution: Universitas Terbuka
course: FSIH4103
source_type: textbook
edition: unknown
checksum: ...
ingested_at: ...
```

### Section

Preserves hierarchical document structure.

```text
Document
├── Module 1
│   ├── Section 1.1
│   └── Section 1.2
└── Module 2
    ├── Section 2.1
    └── Section 2.2
```

Sections are preferred over blind fixed-size chunking whenever document structure can be recovered reliably.

### Concept

A stable semantic entity representing something being learned.

Examples:

```text
Eigenvalue
Kirchhoff's Current Law
First-order ODE
State Sovereignty
Legal Hierarchy
Binary Search
```

A concept should be able to appear in many documents without becoming a duplicate concept each time.

### Claim

Represents a specific proposition supported by a source.

This allows Asterion to distinguish the abstract concept from what individual sources actually say about it.

### Relation

Represents semantic connections between concepts, claims, documents, or external entities.

Example:

```text
Eigenvalue
   ├── prerequisite_for → Linear Dynamical Systems
   ├── related_to       → Eigenvector
   └── appears_in       → Linear Systems
```

### SourceSpan

The smallest useful traceable portion of a source supporting a claim or extracted relation.

Depending on the source, this may point to:

- page range,
- section,
- paragraph,
- slide,
- timestamp,
- line range,
- bounding box,
- or another stable locator.

### LearnerState

Represents the user's relationship to a concept without modifying the concept itself.

Possible fields:

```yaml
concept: Eigenvalue
mastery: 0.58
confidence: 0.44
attempts: 6
last_seen: 2026-08-28
common_errors:
  - determinant setup
```

The exact mastery model is not frozen yet. v1 should prioritize storing reliable learning events before inventing overly precise scores.

---

## Planned Architecture

```text
                         Asterion
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
     Archive             Semantic            Learner
        │                   │                   │
 documents / files      concepts / claims   mastery / history
 metadata               relations           attempts / review
 provenance             source links         confidence
        │                   │                   │
        └──────────────┬────┴───────┬───────────┘
                       │            │
                   Retrieval    Reasoning
                       │            │
                       └─────┬──────┘
                             │
                     Asterion Interface
                             │
               search / tutor / planner / chat
```

A tentative repository shape:

```text
Asterion/
├── asterion/
│   ├── archive/
│   ├── ingestion/
│   ├── semantic/
│   ├── learner/
│   ├── retrieval/
│   ├── reasoning/
│   └── interface/
│
├── integrations/
│   ├── corpusindomicus/
│   ├── starforge/
│   └── arline/
│
├── schemas/
├── tests/
├── docs/
└── README.md
```

This structure is deliberately provisional. v1 should prove the data model and ingestion path before the repository is split into elaborate services nobody asked for.

---

## Asterion in the Parexa Family

Asterion is intended to live in the same broader project family as several related systems while maintaining a distinct responsibility.

```text
Parexa
│
├── Arline
│   └── semantic / narrative intelligence
│
├── Starforge
│   └── general academic platform
│
├── Asterion
│   └── personal academic intelligence + semantic archive
│
└── CorpusIndomicus
    └── authoritative Indonesian legal corpus
```

### Relationship to Starforge

Asterion is conceptually derived in part from the academic-assistant direction explored by Starforge, but it should not simply become a personal configuration of Starforge.

The intended distinction is:

| | Starforge | Asterion |
|---|---|---|
| Primary scope | General academic assistance | Personal academic intelligence |
| User model | Generic / platform-oriented | Persistent individual learner model |
| Archive | Useful subsystem | Core subsystem |
| Semantic graph | Optional / supporting | Central |
| Provenance | Important | Foundational |
| Personal history | Limited | Persistent |
| Cross-course reasoning | General | Deeply personalized |
| Privacy model | Platform concern | Local-first by design |

Useful infrastructure may be reused selectively, but Asterion should not inherit unnecessary platform assumptions merely because code already exists somewhere else.

### Relationship to Arline

Arline has explored semantic representations involving entities, relations, state, memory, and consequence-aware reasoning.

Asterion intends to derive the useful **domain-agnostic semantic ideas** from that work and adapt them to academic knowledge.

Narrative semantics and academic semantics are different domains, but both benefit from explicit entities, state, relations, temporal context, and traceable inference.

### Relationship to CorpusIndomicus

CorpusIndomicus is particularly important for law-related education.

Academic material and authoritative law are not the same thing.

Asterion should eventually be able to represent both:

```text
ACADEMIC SOURCE
"how a textbook explains the law"
          │
          │ compare / ground
          ▼
AUTHORITATIVE SOURCE
"what the legal instrument currently says"
```

This enables use cases such as detecting when a textbook explains a regulation that has since been amended or replaced.

Asterion should never silently overwrite the textbook's historical statement. Instead, it should preserve both sources and surface the discrepancy.

---

## Cross-domain Knowledge

Asterion is explicitly intended to support multiple academic domains in the same personal archive.

For example, engineering and law may connect through concepts such as:

```text
Autonomous Systems
       ↓
Decision Making
       ↓
Liability
       ↓
Legal Responsibility
       ↓
Indonesian Regulation
```

or:

```text
Robotics
   ↓
Sensor Data
   ↓
Data Processing
   ↓
Privacy
   ↓
Legal Framework
```

The goal is not to force artificial connections between every subject. It is to make meaningful bridges discoverable when evidence for them exists.

---

## Ingestion Philosophy

The intended ingestion pipeline is:

```text
original artifact
      ↓
content extraction
      ↓
layout / hierarchy recovery
      ↓
normalized document model
      ↓
section segmentation
      ↓
concept + claim extraction
      ↓
semantic linking
      ↓
indexing
```

Every stage should retain enough provenance to trace a generated semantic object back to the original material.

For scanned or visually complex documents, future integrations may use OCR or visual-document systems, but Asterion itself should define the canonical archive and semantic representation rather than becoming tightly coupled to one OCR engine.

---

## What Asterion Should Eventually Do

### Semantic archive

Given a concept, show:

```text
STATE SOVEREIGNTY

Definition
──────────
...

Relations
─────────
→ State
→ Government
→ Sovereignty Theory

Appears in
──────────
→ Ilmu Negara
→ Module 2
→ Section 2.4
→ pp. 81–88

Source confidence
─────────────────
0.93

Learner state
─────────────
Not assessed
```

### Personalized retrieval

Instead of merely answering a question, retrieval can consider:

- the user's courses,
- previously studied material,
- current mastery,
- prerequisite concepts,
- source authority,
- source recency,
- and cross-course relevance.

### Learning-state tracking

Asterion should eventually record learning events such as:

- concept reviewed,
- question attempted,
- mistake made,
- explanation requested,
- quiz completed,
- confidence self-reported,
- concept revisited after delay.

These events can support a learner model without pretending that a single magical decimal perfectly represents understanding.

### Study planning

Asterion may later recommend what to review based on concept dependencies rather than only calendar deadlines.

For example:

```text
Weak concept: Eigenvalue
        ↓ affects
Linear Systems
        ↓ affects
Differential Equations
        ↓ affects
Numerical Methods
```

The planner could prioritize the weak dependency before downstream topics become harder.

### Source discrepancy detection

For domains with authoritative corpora, Asterion should be able to flag meaningful differences between course material and newer external sources.

Example:

```text
⚠ Course material may be outdated

Academic source:
Textbook explains Regulation X.

Authoritative corpus:
Regulation X was amended by Regulation Y.

Affected concepts:
- Legal hierarchy
- Administrative authority
```

### Cross-domain concept bridges

Asterion may discover that a concept being studied in one discipline has meaningful relationships to another discipline already present in the archive.

These bridges should be evidence-backed and explainable, not generated merely because a language model can invent a connection between two nouns.

---

## v1 Development Strategy

Asterion should begin painfully narrow.

Do **not** start by ingesting an entire university archive, implementing six model backends, building a distributed graph database, and designing a celestial dashboard before a single source can be traced correctly. Software has enough ceremonial suffering already.

The first MVP should ingest **one academic book** reliably.

### v0.1 — Archive Core

Goal:

> One document enters Asterion and comes out as a preserved, queryable hierarchy with stable source references.

Focus:

- document registration,
- immutable source preservation,
- metadata,
- checksum / identity,
- section hierarchy,
- source spans,
- local persistence.

### v0.2 — Semantic Core

Goal:

> Sections can produce concepts and claims while preserving provenance.

Focus:

- concept identity,
- aliases,
- claims,
- relations,
- semantic provenance,
- confidence metadata.

### v0.3 — Retrieval

Goal:

> Ask for a concept and retrieve the best source-backed material.

Focus:

- lexical retrieval,
- semantic retrieval,
- source-aware ranking,
- section/context reconstruction,
- citation / provenance output.

### v0.4 — Learner Model

Goal:

> Track how the user interacts with concepts over time.

Focus:

- learning events,
- attempts,
- review history,
- weakness signals,
- confidence,
- initial mastery estimation.

### v0.5 — Asterion Interface

Goal:

> Provide a persistent interaction layer over the archive.

Possible modes:

- search,
- explain,
- tutor,
- quiz,
- compare sources,
- study planning.

### v0.6 — CorpusIndomicus Bridge

Goal:

> Connect academic legal concepts to authoritative Indonesian legal material.

Focus:

- legal-entity linking,
- source authority classes,
- regulation version awareness,
- discrepancy detection,
- provenance-preserving comparison.

### v0.7 — Parexa-family Integration

Goal:

> Selectively reuse mature ideas or components from Starforge and Arline without collapsing the projects into each other.

Potential areas:

- semantic state representation,
- reasoning primitives,
- academic interaction patterns,
- model adapters,
- shared retrieval utilities.

---

## Initial Success Criteria

Before advanced AI features are considered successful, Asterion should be able to demonstrate all of the following on one real academic source:

1. Preserve the original artifact.
2. Recover a useful document hierarchy.
3. Store stable references to source spans.
4. Extract or manually register a concept.
5. Attach one or more claims to that concept.
6. Trace every stored claim back to its source.
7. Retrieve the concept and reconstruct relevant context.
8. Keep learner state separate from source confidence.
9. Re-ingest without silently duplicating the same document.
10. Export or inspect the stored structure without requiring an LLM.

If these work, Asterion has a trustworthy foundation.

---

## Non-goals for Early v1

Asterion is **not** initially trying to become:

- a universal LMS,
- a university administration platform,
- a generic multi-tenant SaaS,
- an autonomous assignment solver,
- a replacement for authoritative academic or legal sources,
- a giant agent swarm,
- or a chatbot wearing an archive-shaped costume.

The early system should prioritize correctness, provenance, inspectability, and useful personal retrieval.

---

## Design Questions Still Open

The following are intentionally not frozen yet:

- primary implementation language(s),
- graph storage model,
- embedding model,
- local LLM backend,
- exact mastery equation,
- semantic relation ontology,
- document parsing stack,
- OCR integration strategy,
- UI framework,
- cross-project package boundaries,
- synchronization / backup model,
- and whether some Parexa semantic primitives should eventually become a shared standalone library.

These should be decided from prototype evidence rather than aesthetics alone.

---

## Long-term Direction

The long-term vision is a system that can answer three different questions without confusing them:

### What does the source say?

Grounded in preserved academic or authoritative material.

### What does the knowledge mean and how is it connected?

Represented through concepts, claims, relations, and reasoning.

### What does the user know?

Represented through a separate temporal learner model.

Together:

```text
Asterion Knowledge
    = Academic Sources
    + Semantic Structure
    + Provenance
    + Personal Learning State
    + Temporal Context
```

Asterion should become a navigable model of an education rather than merely a folder containing educational files.

The archive stores the stars.

The semantic layer draws the constellation.

Asterion learns how the user navigates it.
