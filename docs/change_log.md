# UniGuide AI — Change Log, version 1.0 → 1.3

Date: 25 Nov 2026. This log lists what changed in each version, why, and which files were affected. A file not listed for a version was not changed in that version.

## 1. Summary

| Version | What changed | Sources | Chunks |
|---|---|---|---|
| 1.0 | Original Week 1–3 submission (five PDFs, S01–S05; run of 10 Nov 2026) | 5 | 284 |
| 1.1 | Dataset replaced: core set (S01, S02, S06, S07, S08) plus S05. S03 and S04 retired. Documents, notebook and manifests regenerated | 6 | 231 |
| 1.2 | Chunk splitter fixed (sentence-aware); wrong explanation in 1.1 corrected; Colab robustness fixes | 6 | 238 |
| 1.3 | S09 added (Semester II work schedule); source URLs and retrieval dates recorded; Timeliness finding resolved | 7 | 260 |

## 2. Version 1.1 — new dataset

**Why:** the course dataset changed to the 2026 core set (S01 calendar, S02 regulations, S06 work schedule, S07 matric card FAQ, S08 UTMID FAQ) plus S05, the senior-student manual. S03 (2010 handbook) and S04 (new-student manual) were retired. Source IDs S01, S02 and S05 kept their meaning.

| Area | Change |
|---|---|
| Scope (Week 1) | Problem statement, evidence 3, O1, target users, six data sources, "different from EduAdvisor", risk 1 and 2 mitigations updated. Scope narrows to full-time bachelor's students and to matric card and UTMID services. |
| Requirements (Week 2) | Scope table, O1, FR1, FR6 boundary and RAI2 extended (time-bound and conflicting sources). Citations cleaned (two verified in Crossref, one marked not verified). |
| Evaluation criteria | Criterion 7 added: expired deadlines and conflicting sources. |
| Architecture decisions | Decision 7 added: source type and precedence rule (regulations for rules, calendar and work schedule for dates, manuals and FAQs for steps and services; show both sources if unresolved). Two traceability rows added. |
| Data inventory, pipeline decisions | Rewritten for the new sources: OCR decisions, conflicts, privacy, new A/B comparison, stage contracts, run results. |
| Pipeline diagram | Redrawn as a draw.io file (OCR branch, new counts). |
| Notebook | New sources and source-specific splitters (calendar blocks, numbered rows, Q&A pairs), targeted OCR cell (S05 page 9 legend; S08 Q6 and Q7), new metadata fields (`source_type`, `doc_date_basis`, `valid_until`, `source_url`, `retrieved_date`, `extraction_method`, `text_fixups`). |
| Manifests, prepared text | Regenerated from the new run. |
| Not changed | Week 1 observation sheet, Week 1 system map, Week 2 architecture picture. |

Dataset facts recorded as known issues: S02 Art. 13 (Dean approval above 18 credits) versus S06 row 7 (Academic Advisor and Deputy Dean); Federal Territory Day 1 Jan (S06) versus 1 Feb (S01); S06 typo "22 Janaury 2026"; "capping" undefined in S02.

## 3. Version 1.2 — splitter fix

| Item | Version 1.1 | Version 1.2 |
|---|---|---|
| S02 chunks starting lowercase | 50 | 18 |
| S02 chunks | 176 | 183 |
| Total chunks | 231 | 238 |
| Chunks under 50 characters | 5 | 6 |

- **Cause:** the splitter for S02 and S05 cut long paragraphs at line wraps, so many chunks began mid-sentence. Version 1.1 wrongly said the cause was sentences split over page breaks. The splitter now cuts only after a sentence end. The 18 chunks that still start lowercase continue a sentence over a page break.
- **Files corrected:** pipeline decisions (§3, §11), data inventory (counts), diagram, notebook, manifests, prepared text.
- **Colab fixes:** the label-rejoin pattern for S08 Q7 accepts any whitespace; if the Q7 label is missing the OCR text attaches to the first chunk on that page with a printed warning; the assertion prints the pypdf version and page text; Cell 1 installs `pypdf>=5.9,<7`. pypdf 3.x extracts S06 and S08 differently and is not supported.
- **Left out on purpose:** ideas from the lecture notes that the lab documents do not require (extra risk lines, extra requirements and criteria, a metadata gate, failure-type demo cell, overlap and status fields).

## 4. Version 1.3 — S09 and recorded sources

**S09 added:** UG Work Schedule, Semester II 2026/2027 (3 pages, 22 rows, same layout as S06). S06 and S09 together cover the whole 2026/2027 session (S06 to 21 Mar 2027, S09 to 3 Oct 2027).

| Item | Version 1.2 | Version 1.3 |
|---|---|---|
| Sources | 6 | 7 |
| Chunks | 238 | 260 (S09 adds 22) |
| S02 share of characters | 86.2% | 82.9% |
| Chunks under 50 characters | 6 | 7 (`S09-p003-02`, the short "Revision Period" row) |
| Prepared file size (student's Colab run) | — | 393,904 bytes |

- **Source URLs and retrieval dates** recorded for all seven sources (notebook Cell 5A0, data inventory, scope). S01 and S05 `doc_date_basis` now cite the file name (session 2026/2027).
- **Files regenerated:** README, scope (source table), requirements (source count and semester note), architecture decisions (source count), data inventory, pipeline decisions, diagram, notebook, manifests, prepared text. Evaluation criteria remain at version 1.1.
- **Topic 4 A2 quality check:** script added. Timeliness is tested against a stated requirement (time-bound schedules unexpired and together reaching the end of the session) and passes. A new finding records that S06 and S09 have near-identical rows, so answers must name the semester.
- **S09 source issues noted, not corrected:** row 1 says registration "in January 2026"; row 17 is titled "Class Schedule for Semester I, 2026/2027"; row 9 has the typo "Submisssion". The printed date is "September 2026" with no day, so the PDF creation date is used as `doc_date`.

## 5. Files that never changed (kept from version 1.0)

`week1_observation_sheet.md`, `week1_system_map.png`, `week2_architecture_v1.png`.

