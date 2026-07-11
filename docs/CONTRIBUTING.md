# Contributing to the Documentation

This repository is documentation-first. During the Architecture Phase, the primary deliverable is precise, internally consistent engineering documentation — not code. This guide defines how documentation must be written so that every document is predictable, reviewable, and usable as an implementation contract.

## Core Principle

Documentation is the source of truth. Code implements the documentation, not the other way around. If a document and the code disagree, the document is either updated deliberately or the code is corrected — the two must never silently diverge.

## Required Section Order

Every engineering document must begin with the following sections, in this order:

1. **Purpose** — one or two sentences stating why the document exists and what it covers.
2. **Goals** — what this document and the subsystem it describes are trying to achieve.
3. **Non Goals** — what is explicitly out of scope, to prevent scope creep and false assumptions.
4. **Design Decisions** — the key choices made, with brief rationale; link to ADRs in `adr/` for significant ones.
5. **Implementation Notes** — practical guidance for whoever implements the specification.
6. **Acceptance Criteria** — concrete, checkable conditions that define "done" for this document and its implementation.

Longer subsystem specifications should use the full `DOCUMENT_TEMPLATE.md`, which expands on these sections. The six sections above are the minimum every document must contain.

## Writing Rules

Write in plain, direct prose and prefer clarity over cleverness. Define every domain term in `00-foundation/GLOSSARY.md` before using it widely, and use terms consistently — for example, `Worker` internally and `Agent` in customer-facing copy. State assumptions explicitly rather than leaving them implicit. Keep each document focused on a single subsystem or concern; link to related documents instead of duplicating their content. Every claim about behavior should be specific enough to test.

## Design Decisions and ADRs

Record narrative, running decisions in `00-foundation/DECISIONS.md`. When a decision is significant, architectural, and worth referencing on its own, also create a numbered ADR in `adr/`. ADRs are immutable once accepted: to change a decision, add a new ADR that supersedes the old one rather than editing history.

## Review Checklist

Before opening a pull request for documentation, confirm that: the required sections are present and ordered; goals and non-goals are explicit; new terms are in the glossary; significant decisions are recorded as ADRs; acceptance criteria are concrete and checkable; and the document does not introduce architecture that conflicts with `00-foundation/MASTER_ARCHITECTURE.md` or the decision log without updating them first.
