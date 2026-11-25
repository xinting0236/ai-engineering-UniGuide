**Version 1.3 change:** *adds S09 (UG Work Schedule, Semester II 2026/2027), a companion to S06 with the same 22-row layout. Counts: 238 → 260 records, 6 → 7 sources. Version 1.2 (kept): S02 and S05 chunks are cut only after a sentence end (S02 176 → 183 chunks; chunks starting lowercase 50 → 18; v1.1 wrongly blamed page breaks).*

# Week 3 — Pipeline Decisions, Stage Contracts and Traceability — UniGuide AI

**Project:** UniGuide AI — AI-Powered University FAQ Assistant
**Pathway:** A — Documents (PDF)
**Notebook:** `notebooks/week3_pipeline_version1.3.ipynb` (run of 24 Nov 2026 on the seven-source dataset; the v1.0 run was 10 Nov 2026)

---

## 1. Topic 2 requirements ID(s)

| Requirement | What the pipeline provides |
|---|---|
| FR1 / FR2 | Seven official 2026 UTM documents (calendar, regulations, senior-student manual, work schedules for Semester I and II, matric card FAQ, UTMID FAQ) turned into retrievable chunks. |
| FR3 / RAI1 | Every chunk keeps source ID, PDF page, printed page and section (row number for S06 and S09, Q number for S07 and S08), so an answer can be cited. |
| RAI2 | Every chunk carries `doc_date` with its basis, `edition`, `currency_status` and, for time-bound sources, `valid_until`. |
| FR1 / RAI1 (conflicts) | `source_type` (`rules`, `dates`, `how_to`, `faq`) supports the precedence rule in ADR Decision 7. |
| NFR4 | One configuration entry and one SHA-256 per source, so one updated document can be reprocessed alone. |

## 2. Selected sources

Full details: `docs/week3_data_inventory_version1.3.md`. Raw files are not committed.

| ID | Source | Pages | Characters | Version / date |
|---|---|---|---|---|
| S01 | Academic Calendar 2026/2027 (English) | 1 | 3,591 | Undated; PDF printed 20 Nov 2026 |
| S02 | Academic Regulations, Full-Time UG | 81 | 105,132 | 2026 |
| S05 | Senior-student registration manual | 10 | 2,049 | 6 Oct 2026 |
| S06 | UG Work Schedule, Semester I 2026/2027 | 3 | 5,041 | 9 Oct 2026 |
| S09 | UG Work Schedule, Semester II 2026/2027 | 3 | 4,937 | Sep 2026 (no day printed); PDF created 10 Sep 2026 |
| S07 | FAQ Matric Card | 2 | 3,623 | 1 Oct 2026 |
| S08 | FAQ UTMID | 2 | 1,824 | 29 Sep 2026 |

Total extracted text: 126,197 characters. Retired: S03 (2010 handbook), S04 (new-student manual).

## 3. Part B comparison and evidence

**Approach A:** fixed 500-character chunks over the whole document.
**Approach B:** page-aware, source-specific splitting: paragraph/sentence packing for S02 and S05, semester blocks for S01, numbered rows for S06 and S09, Q&A pairs for S07 and S08.

| Source | A chunks | A crossing a page break | A starting lowercase | A cutting a row / Q&A / block in two | B chunks | B crossing a page break | B starting lowercase |
|---|---|---|---|---|---|---|---|
| S01 calendar | 7 | 0 | 3 | 4 of 5 blocks | 5 | 0 | 0 |
| S02 regulations | 205 | 71 | 156 | n/a | 183 | 0 | 18 |
| S05 senior manual | 4 | 4 | 1 | n/a | 10 | 0 | 0 |
| S06 work schedule | 10 | 2 | 7 | 9 of 22 rows | 22 | 0 | 0 |
| S09 work schedule (Sem II) | 10 | 2 | 5 | 9 of 22 rows | 22 | 0 | 0 |
| S07 matric FAQ | 8 | 1 | 6 | 7 of 11 Q&A | 11 | 0 | 0 |
| S08 UTMID FAQ | 4 | 1 | 2 | 3 of 7 Q&A | 7 | 0 | 0 |
| **Total** | **248** | **81** | **180** | | **260** | **0** | **18** |

(The "cutting" column counts how many B units a fixed 500-character window would split.)

**Selected approach: B.**

- **Benefit:** each deadline row, Q&A pair and calendar block stays whole, so an answer and its date or penalty stay together. No chunk crosses a page break. Image-only and blank pages are excluded.
- **Accepted limitation:** B is not semantic chunking. In S02, 18 of 183 chunks still start lowercase: a sentence that runs over a page break is split in two, because pages are never merged. Table column structure in S01 and S06 is not preserved (only rows or blocks are).

## 4. Chosen preparation configuration

- Whitespace normalised. Repeated footers, title lines and `/gid` glyph codes (75 on S02's title page) removed.
- Pages with fewer than 20 characters skipped: S02 pages 1, 2, 3, 11, 13, 15, 81. No page skipped in the other six sources.
- Maximum chunk size: S02 800, S05 1,200; S01, S06, S09, S07 and S08 are split by structure (block, row, Q&A pair).
- Fix-ups (logged per record in `text_fixups`): S01 rejoins the split word "2 Au / gust 2027"; S06 removes the page-number line and separates row 3 from row 2 where extraction glued them; S08 rejoins the "Q7 … :" label.
- S06 and S09 rows are split only where the next expected row number appears, which avoids false splits at dates such as "10 November 2026". The run found all 22 rows in each of the two schedules.
- S02: printed page read from each page's number line (offset 17 as a fallback, flagged `printed_page_inferred`). No mismatch with the offset was found.
- OCR: only three regions (S05 p9 legend, S08 Q6 and Q7). The checked text is appended to the page's chunk with the label "[Text read from page image]" and `extraction_method` records how it was obtained. S05 p3 and p8 are never OCR'd.
- The PDF Author metadata field is never copied.

## 5. ETL / ELT decision

**ETL.** Text is cleaned, chunked and enriched before it is stored in `prepared_text_version1.3.jsonl`. The corpus is small (about 123,000 characters) and retrieval needs clean, cited chunks.

## 6. Batch versus streaming

**Batch.** These documents change rarely. One new fact in v1.1: the work schedules are replaced each semester (S06 for Semester I, S09 for Semester II), so the batch re-run must happen at each semester start (NFR4: one document within 10 minutes; the run processes one configuration entry per document).

## 7. Stage contracts

| Stage | Contract / evidence | Failure response |
|---|---|---|
| Ingest | All seven named files exist in `data/raw` and have a SHA-256. | Stop with "Source file not found". |
| Inspect | Each PDF has extractable text; pages, characters, low-text pages, image pages and `/gid` tokens are reported. | Stop if a source has zero extractable text. Flag low-text pages. |
| Prepare | Every source produces chunks; the expected unit counts hold (S01 5 blocks, S06 22 rows, S09 22 rows, S07 11 Q&A, S08 7 Q&A); record IDs are unique; no `/gid` codes; no retired source in the corpus; OCR output contains no matric or 12-digit ID pattern. | Stop with a message naming the source. |
| Validate | `prepared_text_version1.3.jsonl` exists, is non-empty, and has a SHA-256. | Stop for a missing or empty output. |
| Record lineage | Each source is mapped to the output; manifests store checksums, processing note, `source_type`, `valid_until`, OCR supplement IDs, retired sources, rights and limitations. | Stop or report a missing mapping or metadata. |
| Repeatability (Cell 9) | A second run gives identical source fingerprints, output fingerprint and mapping. | Assertion error naming what changed. |

## 8. Results of the Colab run

|  Check | Result |
|---|---|
| Part B output (`prepared_text_version1.3.jsonl`) | 260 records, 393,807 bytes |
| Records per source | S01 5, S02 183, S05 10, S06 22, S09 22, S07 11, S08 7 |
| Share of prepared text by characters | S02 82.9%, S06 3.9%, S09 3.8%, S07 2.8%, S01 2.7%, S05 2.1%, S08 1.7% |
| Extraction methods | pypdf 257; pypdf + OCR with manual check 1; pypdf + manual transcription 2 |
| Part B validation (Cell 5A2) | PASS |
| Lineage manifest (Cells 6A and 6B) | 6 entries, `COMMON LINEAGE VALIDATION: PASS` |
| Part C pipeline (Cell 8A) | `SUCCESS: documents`, 6 inputs, 1 output |
| Part C output compared with Part B | **Identical** |
| Repeatability (Cell 9) | `PASS: documents` |
| Duplicates | 0 exact-duplicate chunk texts; 0 duplicate IDs |
| Chunks under 50 characters | 7: `S02-p004-01` (24), `S02-p007-01` (11), `S02-p016-02` (37), `S02-p069-02` (15), `S02-p071-04` (46), `S05-p008-01` (49), `S09-p003-02` (45, the short "Revision Period" row) — for Week 4 review |
| Privacy scan | 6 UTM office or example e-mail addresses; 0 phone numbers, 0 12-digit IDs, 0 matric numbers |
| `source_url`, `retrieved_date` | Filled for all seven sources (dates and URLs entered by the student; see the data inventory) |

## 9. Output locations

| Artefact | Location |
|---|---|
| Prepared chunks | `data/processed/prepared_text_version1.3.jsonl` |
| Lineage manifest | `data/week3_manifest_version1.3.json` |
| Pipeline output and manifest | `data/pipeline_v1/documents/prepared_text_version1.3.jsonl` and `pipeline_manifest_version1.3.json` |
| Notebook | `notebooks/week3_pipeline_version1.3.ipynb` |
| Data inventory | `docs/week3_data_inventory_version1.3.md` |
| Diagram | `docs/week3_pipeline_diagram_version1.3.png` |

## 10. Provenance and access caveats

- All sources are public official UTM documents used for coursework. The manifest's `access_rights` is a statement from the inventory, not a legal check of redistribution rights.
- Raw PDFs and the full `prepared_text_version1.3.jsonl` should not be published until UTM's redistribution terms are checked. In v1.0 the full file was committed; remove it from the public repository or replace it with a small sample.
- SHA-256 shows that a file did not change between runs. It does not show that the document is current or correct.
- S05 is labelled `current_2026_student_verified` on the student's word; record the verification evidence (date and how it was checked). S06 is `current_2026_time_bound`. S01 has no publication date, so its `doc_date` is the PDF print date.
- S05 contains screenshots with 2024 session labels (not indexed).

## 11. Known quality issues carried forward to Topic 4

1. Conflicts between sources (Dean vs Deputy Dean; Federal Territory Day 1 Jan vs 1 Feb; "capping" undefined in S02).
2. S09 row 1 says registration "in January 2026" and row 17 is titled "Class Schedule for Semester I, 2026/2027" inside a Semester II schedule (probably copied from the older text; kept verbatim and noted on the chunk). S06 typo "22 Janaury 2026" and the blue-marked revision in row 3 (colour lost).
3. 18 S02 chunks start mid-sentence (sentences continuing over a page break); 7 chunks are under 50 characters.
4. Deadlines in S06 that have already passed (time-bound content). S06 covers Semester I and S09 covers Semester II, so a question must name the semester; rows with the same number are near-identical in wording (about 7 pairs with word overlap of 0.5 or more).
5. S02 is 86% of the corpus; student-service coverage is only matric card and UTMID, so questions on other services will (correctly) hit the fallback.
6. OCR text for S05 p9 and S08 Q6/Q7 is checked once by one person; the "PTF" label is unverified.

## 12. Traceability (extends `week2_architecture_decisions_version1.3.md`)

| Objective | Requirement | Architecture element | Week 3 evidence |
|---|---|---|---|
| O1 | NFR4 / RAI2 | Document Management and Knowledge Base Update | Configuration entry and SHA-256 per source; `week3_manifest_version1.3.json`; Cell 9 repeatability PASS. |
| O1 | FR1 / FR2 | Knowledge Source | `prepared_text_version1.3.jsonl`: 7 sources, 260 records. |
| O2 | FR3 / RAI1 | Citation and Source Control (input data) | `pdf_page`, `printed_page` and `section` on every record. |
| O2 | RAI2 | Citation and Source Control (input data) | `doc_date`, `doc_date_basis`, `edition`, `currency_status`, `valid_until` on every record. |
| O2 | FR1 / RAI1 (Decision 7) | Citation and Source Control (precedence rule) | `source_type` on every record. |
