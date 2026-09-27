# research-outline-builder
A tool for researchers to plan papers before writing, build a reorder-able outline and attach sources to each section as you find them.

## The Problem

Researchers collect sources way before they start writing, and it's easy to lose track of which source goes where, especially since the structure of the paper is usually still changing at that point. Sources live in one tool, the outline lives in your head or a doc somewhere, and the draft lives somewhere else entirely, with nothing tying them together.

## Core Functionality

- Build an outline made up of sections (Introduction, Literature Review, Methodology, Results, Discussion, Conclusion, or custom ones)
- Reorder sections at any time as the structure of the paper changes
- Attach resources to each section as you find them. A resource can be:
  - A journal article
  - A news article
  - Media (image, video, dataset, etc.)
  - A custom placeholder for something you haven't sourced yet (e.g. "need a bar chart comparing X and Y")

## Data Model

Three connected pieces:

- **Outline** – the paper itself
- **Section** – ordered pieces of the outline, each with a list of resources
- **Resource** – has a type field (journal article, news article, media, or placeholder), since each type needs different info attached (author/year/DOI for a journal article vs. a short note for a placeholder)

## Interface

The main screen is the outline view: a reorderable list of sections you can open and edit, with resources for each section listed underneath. Adding a resource opens a form where you pick the type first, then fill in the fields that make sense for that type.

## Planned Integrations (stretch goals)

- **Mendeley** – pull sources directly from an existing reference library instead of typing them in manually
- **Word export** – export the outline into a Word doc as a starting draft, with sections and resource notes already laid out

## Similar Tools

Zotero and Mendeley manage sources but aren't built around an outline. Scrivener has an outline view but doesn't track sources by section or connect to a reference manager. This project sits in the middle: outline first, sources attached at the section level, eventually linked to the tools people already use.

## Status

Early planning stage. Core outline and resource model coming first, integrations later in the semester.

