---
type: decision
relevance: high
last_reviewed: 2026-03-16
---
# DEC-001 - Concordance data format

**Status:** Accepted
**Date:** 2026-03-16
**Scope:** Concordance project

## Context
The Concordance project needs a data format for storing recipe-to-spectrum mappings. Marta suggested using a SQLite database for queryability. Tobias mentioned Airtable. I needed to decide before the dataset grows past 50 entries.

## Decision
Use Markdown files (one per entry) with structured headings + companion CSV files for spectral data. No database.

## Alternatives considered
- **SQLite database** — Better for queries, but adds a tooling dependency. I'd need a separate app to view/edit entries, breaking the "everything in Obsidian" principle. Marta doesn't use Obsidian, but she can read CSVs.
- **Airtable** — Good UI, but proprietary, cloud-dependent, and I'd lose version control. Also can't embed wikilinks or connect entries to my research notes.
- **JSON files** — Machine-readable but painful to edit by hand. Markdown is both human-readable and parseable enough for my Python scripts.

## Consequences
- Entries are readable in Obsidian with full wikilink support — they're part of the knowledge graph
- CSV files for spectral data sit alongside the Markdown and are loaded by Python scripts
- Querying across entries requires grep or a Python script, not SQL — acceptable at current scale (< 200 entries)
- If the dataset grows past 500 entries, revisit this decision
