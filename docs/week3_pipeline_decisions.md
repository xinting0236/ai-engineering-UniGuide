# Week 3 — Pipeline Decisions, Stage Contracts and Traceability — UniGuide AI

**Project:** UniGuide AI — AI-Powered University FAQ Assistant
**Pathway:** A — Documents (PDF)
**Notebook:** `notebooks/week3_pipeline.ipynb` (Colab run, 10 November 2026)

---

## 1. Topic 2 requirements ID(s)

| Requirement | What the pipeline provides |
|---|---|
| FR1 / FR2 | Official UTM documents (calendar, regulations, handbook, registration manuals) turned into retrievable text chunks. |
| FR3 / RAI1 | Every chunk keeps source ID, PDF page, printed page and section, so an answer can later be cited. |
| RAI2 | Every chunk carries document date, edition and a `currency_status` label (`current_2026` or `currency_not_verified`). |
| NFR4 | One configuration entry and one SHA-256 checksum per source, so a single updated document can be reprocessed without rebuilding the others. |

## 2. Selected sources

Full details are in `docs/week3_data_inventory.md`. Raw files are in `data/raw` and are not committed.

| ID | Source | Pages | Characters | Version / date |
|---|---|---|---|---|
| S01 | Academic Calendar 2026/2027 (corrected version) | 2 | 8,335 | 14 May 2026 |
| S02 | Academic Regulations, Full-Time Undergraduate, 16th Edition | 81 | 105,131 | 2026 |
| S03 | Academic Advising: Ensuring Student Success | 28 | 39,859 | 2010 |
| S04 | New-student course registration manual | 24 | 3,752 | Created 5 Oct 2026 |
| S05 | Senior-student course registration manual | 10 | 2,048 | Created 6 Oct 2026 |

Total extracted text: 159,125 characters.

## 3. Part B comparison and evidence

**Approach A:** fixed 500-character chunks over the whole document (lab default), which ignores pages and structure.
**Approach B:** page-aware paragraph and sentence packing, with source, page, section and date metadata added.

| Source | A chunks | A chunks crossing a page break | A chunks starting lowercase | B chunks | B crossing a page break | B chunks starting lowercase |
|---|---|---|---|---|---|---|
| S01 calendar | 17 | 1 | 9 | 8 | 0 | 0 |
| S02 regulations | 205 | 71 | 164 | 176 | 0 | 50 |
| S03 handbook | 73 | 24 | 56 | 69 | 0 | 8 |
| S04 new-student manual | 7 | 7 | 5 | 21 | 0 | 0 |
| S05 senior-student manual | 4 | 4 | 1 | 10 | 0 | 0 |
| **Total** | **306** | **107** | **235** | **284** | **0** | **58** |

**Selected approach: B (page-aware paragraph/sentence packing).**

- **Benefit:** every chunk can be cited to a source and page (FR3, RAI2). None of the 107 page-crossing chunks that A produces occur in B. Image-only pages are excluded, and the calendar's semester blocks stay together.
- **Accepted limitation:** B is not semantic chunking. A sentence that runs over a page break is split in two, which is why 58 of 284 B chunks start lowercase (50 of them in S02). Table rows inside the calendar and all screenshot content in S04 and S05 are still not captured.

## 4. Chosen preparation configuration

- Whitespace normalised. Repeated footers (`www.utm.my`, the handbook's page footers), running headers and `/gid` glyph codes (75 on S02's title page) removed.
- Pages with fewer than 20 characters skipped: S02 pages 1, 2, 3, 11, 13, 15, 81; S03 pages 1 and 28; S04 pages 1, 2, 5.
- Maximum chunk size: S01 1,200 (page 1 kept as semester blocks), S02 800, S03 800, S04 1,200, S05 1,200 characters.
- S02: printed page read from each page's number line (offset 17 used as a fallback and flagged `printed_page_inferred`); section carried from the running header. No mismatches against the offset were found in the run.
- S01 page 2 (Malay) tagged `language: ms` with a reliability note, because it contains date errors. S03, S04 and S05 are tagged `currency_not_verified`.
- The PDF Author metadata field is never copied.

## 5. ETL / ELT decision

**ETL.** Text is cleaned, chunked and enriched with metadata before it is stored in `prepared_text.jsonl`. The corpus is small (about 159,000 characters), and the downstream retrieval step needs clean, cited chunks. Storing raw text and transforming it later would add steps without benefit.

## 6. Batch versus streaming

**Batch.** UTM publishes these documents rarely (calendars once a year, regulations by edition), so re-running when a document changes is enough. NFR4 asks for a single document update within 10 minutes, and the pipeline processes one document per configuration entry. Streaming would add complexity without benefit.

## 7. Stage contracts

| Stage | Contract / evidence | Failure response |
|---|---|---|
| Ingest | All five named files exist in `data/raw` (`resolve_source`) and each has a SHA-256 checksum. | Stop with "Source file not found". |
| Inspect | Each PDF is readable with extractable text; pages, characters, low-text pages and `/gid` tokens are reported. | Stop if a source has zero extractable text (scanned, needs OCR). Flag low-text pages. |
| Prepare | Every source produces at least one chunk; each record has text, source ID and PDF page; no `/gid` codes remain. | Stop with a message naming the source. |
| Validate | `prepared_text.jsonl` exists, is non-empty, and has a SHA-256 fingerprint. | Stop for a missing or empty output. |
| Record lineage | Each source is mapped to the exact output; `pipeline_manifest.json` and `week3_manifest.json` store checksums, processing note, version, rights and limitations. | Stop or report a missing mapping or metadata. |
| Repeatability (Cell 9) | A second run gives identical source fingerprints, output fingerprint and mapping. | Assertion error naming what changed. |

## 8. Results of the Colab run

| Check | Result |
|---|---|
| Part B output (`prepared_text.jsonl`) | 284 records, 321,628 bytes |
| Records per source | S01 8, S02 176, S03 69, S04 21, S05 10 |
| Part B validation (Cell 5A2) | PASS |
| Lineage manifest (Cells 6A and 6B) | 5 entries, `COMMON LINEAGE VALIDATION: PASS` |
| Part C pipeline (Cell 8A) | `SUCCESS: documents`, 5 inputs, 1 output, manifest written |
| Part C output compared with Part B | **Identical** (no differences to explain) |
| Repeatability (Cell 9) | `PASS: documents`, source fingerprints, prepared output and mappings match |
| Representative record checked | `S02-p040-01`: PDF page 40, printed page 23, Section 4, `current_2026` |

## 9. Output locations

| Artefact | Location |
|---|---|
| Prepared chunks (Part B) | `data/processed/prepared_text.jsonl` |
| Lineage manifest (Part B) | `data/week3_manifest.json` |
| Pipeline output and manifest (Part C) | `data/pipeline_v1/documents/prepared_text.jsonl` and `pipeline_manifest.json` |
| Notebook | `notebooks/week3_pipeline.ipynb` |
| Data inventory | `docs/week3_data_inventory.md` |
| Diagram | `docs/week3_pipeline_diagram.png` |

## 10. Provenance and access caveats

- All sources are public official UTM documents, used for coursework. The manifest records `source_version` and `access_rights` as statements from the inventory, not as a legal check of redistribution rights.
- Raw PDFs and the full `prepared_text.jsonl` are not committed to the public repository, because UTM's redistribution terms were not checked. The repository holds the notebook, manifests, diagram and a small sample.
- SHA-256 shows that a file did not change between runs. It does not show that the document is current or correct.
- S03 (2010 edition) and S04 and S05 (screenshots show 2024 sessions) are labelled `currency_not_verified`. Answers that rely on them must show that label (RAI2).
- S02's printed page numbers are read from the pages themselves. Any page where the number line was not found uses the offset of 17 and is flagged `printed_page_inferred`.

## 11. Known quality issues carried forward to Topic 4

1. S01 page 2 (Malay) contains date errors, so page 1 (English) is the primary source.
2. Table rows in S01 and all screenshots, tables and the slip in S04 and S05 are not captured. OCR would be needed.
3. 58 chunks start mid-sentence because a sentence continues over a page break.
4. S03 describes older systems (AIMS 2000) that newer documents replace, so conflicts with newer sources must be resolved in favour of the newer source.

## 12. Traceability (extends `week2_architecture_decisions.md`)

| Objective | Requirement | Architecture element | Week 3 evidence |
|---|---|---|---|
| O1 | NFR4 / RAI2 | Document Management and Knowledge Base Update | Configuration entry and SHA-256 per source; `week3_manifest.json`; Cell 9 repeatability PASS. |
| O1 | FR1 / FR2 | Knowledge Source | `prepared_text.jsonl`: 5 sources, 284 records. |
| O2 | FR3 / RAI1 | Citation and Source Control (input data) | `pdf_page`, `printed_page` and `section` on every record. |
| O2 | RAI2 | Citation and Source Control (input data) | `doc_date`, `edition` and `currency_status` on every record. |
