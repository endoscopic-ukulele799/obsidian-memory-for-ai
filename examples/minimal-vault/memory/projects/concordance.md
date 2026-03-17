---
type: project
relevance: high
last_reviewed: 2026-03-15
---
# Concordance

**Codename:** Concordance
**Status:** Active
**Location:** `Concordance/` folder in vault + Python scripts in `~/code/concordance-tools/`
**Stack:** Obsidian (notes), Python (data processing), Zotero (bibliography)

## What it is
A systematic mapping between historical pigment recipes (from medieval and Renaissance treatises) and their modern spectroscopic signatures (XRF + FORS). The goal: when I see an XRF peak pattern on an unknown painting, I can match it not just to a pigment, but to a *specific preparation method* described in a period source.

## Current status
- 34 entries completed (recipe → spectrum → identification)
- Vermilion entry blocked: Cennini describes a dry-process vermilion that doesn't match the Ghent XRF profile — need to test wet-process variant
- Lead-tin yellow type I vs type II distinction needs more reference spectra
- Marta (KIK-IRPA) providing binding media data for the next 10 entries

## Notes for the AI
- Each Concordance entry follows a fixed template: Source → Transcription → Modern interpretation → Spectral match → Confidence level
- Confidence levels: `confirmed` (multiple spectral matches), `probable` (single match + literature support), `tentative` (plausible but unverified)
- Don't suggest database software — the Markdown + CSV approach is a deliberate decision (see DEC-001)

## Links relacionados
- [[marta-delvaux]] — supports: primary collaborator on binding media analysis
- [[gallery-work]] — applies: Concordance IDs are used in condition survey pigment identifications
- [[DEC-001 - Concordance data format]] — source: rationale for Markdown + CSV over database
