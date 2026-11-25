**Version 1.3 change:** *S09 (UG Work Schedule, Semester II 2026/2027) added; 7 sources, 260 records (v1.2: 238). Version 1.2 change kept: sentence-aware splitter*


# Week 3 — Data Inventory — UniGuide AI

**Project:** UniGuide AI — AI-Powered University FAQ Assistant
**Pathway used:** A — Documents (PDF). This is the real project dataset, not the synthetic practice pack.
**Why versions 1.1 to 1.3:** the dataset changed from five PDFs (S01–S05, run of 10 Nov 2026) to six 2026 PDFs, and in version 1.3 to seven (S09 added). Inspection and pipeline run: 24 Nov 2026.
**Retrieval dates and URLs:** recorded for all seven sources in SOURCES (notebook Cell 5A0) and in the table below. S02 and S05 were retrieved 10 Nov 2026, S01, S06, S07 and S08 on 20 Nov 2026 and S09 on 24 Nov 2026.

---

## A1 — From Topic 2 requirements to data needs 

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| FR1 / FR2 | Academic calendar dates, registration deadlines and penalties | S01, S06, S09 | Official 2026 documents. S06 covers Semester I and S09 Semester II of 2026/2027. |
| FR1 / FR2 | Academic rules (credit load, grade repeat, fees, examinations) | S02 | Authoritative; 81 pages with a clean text layer. |
| FR1 / FR2 | Course registration steps | S05 | Slide deck; screenshots not extracted. |
| FR1 / FR2 | Matric card and UTMID service answers | S07, S08 | Short Q&A documents with a printed update date. |
| RAI2 | Source date, edition, validity and retrieval date | Printed dates, PDF metadata, `valid_until`, my retrieval record | Prevents expired or old content from being treated as current. |
| NFR4 | Source file identity and version | SHA-256 per file in the manifest | Allows one document to be updated without rebuilding the others. |

---

## A4 — Data inventory checkpoint

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `utm_academic_calendar_2026_2027_1.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/05/Final_Kalendar-Akademik-Sarjana-Muda.Sarjana.Kedoktoran-20262027-14.5.2026-pembetulan.pdf (retrieved 2026-11-20) | PDF, one-page table; `source_type: dates`. | Academic Calendar 2026/2027, Bachelor, Master and Doctoral. **No publication date on the page.** PDF created 20 Nov 2026 by a browser print (PDFium). `doc_date` = 20 Nov 2026 with basis "pdf_creation_date". `valid_until` 3 Oct 2027. Owner: UTM. SHA-256 `7cfd417f62a1…`. | Public official UTM document; coursework use only; raw file not redistributed. | 1 page, 3,591 characters. Text layer present. English only. The table is flattened to a text stream. One word is split across a line (`2 Au / gust 2027`), repaired by a fix-up. |
| S02 | `utm_academic_regulations.pdf` — https://ugs.utm.my/wp-content/uploads/sites/8/2026/05/FT-UG-ACADEMIC-REGULATIONS-16th-edition.pdf (retrieved 2026-11-10) | PDF book; `source_type: rules`. | Title page (read by OCR in this review; pypdf returns glyph codes): "Full-Time Undergraduate Studies Programme, 16th Edition"; copyright page: "First Edition 2026". **The edition label conflicts; both are recorded.** PDF created 7 May 2026. SHA-256 `d2743dfad87d…`. | Public official UTM publication, copyright reserved; coursework use only. | 81 pages, 105,132 characters, 75 `/gid` glyph codes (title page). Low-text pages 1, 2, 3, 11, 13, 15, 81 (blank or divider pages; OCR returned 0 characters, nothing lost). PDF page = printed page + 17. |
| S05 | `utm_User-Manual_Course-Registration-Senior-Student_update202620271.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/10/User-Manual_Course-Registration-Senior-Student_update202620271.pdf (retrieved 2026-11-10) | PDF slide deck; `source_type: how_to`. | "Update 2026/2027". PDF created 6 Oct 2026. Owner: AMD, UTM. Currency verified by the student (evidence: to record). SHA-256 `1f37688b7836…`. | Public UTM document; coursework use only. | 10 pages, 2,049 characters. Every page has some text; the slip (p9), portal screenshot (p3) and sample course list (p8) are images. The deck has **no credit-limit information** (that is in S02 and S06). |
| S06 | `utm_ug_work_schedule_2026_2027.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/10/Student-202620271-UG-Work-Schedule-for-Course-Registration.Award-of-Degree.Examination-1.pdf (retrieved 2026-11-20) | PDF table, 22 numbered rows; `source_type: dates`. | UG Work Schedule for Course Registration, Award of Degree, Examination, **Semester I 2026/2027**. Printed "Date: 9 October 2026". PDF created 10 Sep 2026, modified 9 Oct 2026. Owner: Academic Management Division. `valid_until` 21 Mar 2027. SHA-256 `2ef20c741269…`. | Public official UTM document; coursework use only. | 3 pages, 5,041 characters. Text layer present. Rows split cleanly into 22 chunks. |
| S09 | `utm_ug_work_schedule_sem2_2026_2027.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/09/Student-202620272-UG-Work-Schedule-for-Course-Registration.Award-of-Degree.Examination.pdf (retrieved 2026-11-24) | PDF table, 22 numbered rows; `source_type: dates`. | UG Work Schedule for Course Registration, Award of Degree, Examination, **Semester II 2026/2027**. Printed "Date: September 2026" (no day). PDF created 10 Sep 2026; `doc_date` = 2026-09-10 with that basis. Owner: Academic Management Division. `valid_until` 3 Oct 2027 (end of the Semester II break). SHA-256 `28b3e102cb7f…`. | Public official UTM document; coursework use only. | 3 pages, 4,937 characters. Clean text layer, same layout as S06. All 22 rows found. Row 1 says registration "in January 2026" and row 17 is titled "Class Schedule for Semester I, 2026/2027"; both look copied from older text (kept verbatim). |
| S07 | `utm_matric_card_faq_2026.pdf` — https://studentaffairs.utm.my/wp-content/uploads/2026/10/FAQs-Kad-Matrik-2026.pdf (retrieved 2026-11-20) | PDF, 11 Q&A pairs; `source_type: faq`. | "Updated 1 Oktober 2026". PDF created 20 Nov 2026 (print). SHA-256 `0309ec341777…`. | Public UTM document; coursework use only. | 2 pages, 3,623 characters. Clean text. Contains office e-mail addresses only. |
| S08 | `utm_utmid_faq_2026.pdf` — https://studentaffairs.utm.my/wp-content/uploads/2026/09/UTMID-2026.pdf (retrieved 2026-11-20) | PDF, 7 Q&A pairs; `source_type: faq`. | "Updated on 29th September 2026". PDF created 20 Nov 2026 (print). SHA-256 `937012aea75d…`. | Public UTM document; coursework use only. | 2 pages, 1,824 characters. Q1–Q5 are text. **Q6 and Q7 answers are infographic images** (OCR target). |


Total extracted text, seven sources: 126,197 characters (S02 is 83% of the prepared corpus by characters).

---

## Inspection evidence (representative sources)

- **S06 row 10** (`S06-p002-02`): "10 Course Withdrawal by students 5 weeks 4 – 8 / 2 November - 4 December 2026 …" The row stays whole, with its dates and the warning that a student who withdraws after the amendment deadline still pays.
- **S01 Semester I block** (`S01-p001-02`): starts at the heading "SEMESTER I | 12 OCTOBER 2026 – 21 MARCH 2027 (23 WEEKS)" and keeps all its dates together.
- **S08 Q6** (`S08-p001-06`): the pypdf text ends at "Here's how to UTMSmart :". The four steps come from the image and are appended with the label "[Text read from page image]".
- **S02 page 40** (`S02-p040-01`): PDF page 40 = printed page 23, Section 4.

---


## Ingestion limitations observed

1. **Conflicts between sources.** S02 (Art. 13) says more than 18 credits needs Dean approval; S06 row 7 names the Academic Advisor and the Deputy Dean. S06 row 14 labels 1 Jan 2027 as "Federal Territory Day – KL only", while S01 lists 1 Feb 2027 as Federal Territory Day and 1 Jan as New Year's Day (KL only). S05 says "Capping will be counted after successful submission and approved"; S02 does not define capping. Precedence rule: see ADR Decision 7.
2. **S06 typo.** Thaipusam is printed "22 Janaury 2026"; S01 gives 22 January 2027. The text is kept verbatim and the record carries a reliability note.
3. **S06 row 3 shows two date ranges.** The PDF marks the revised range in blue; the colour is lost in text extraction. Treat the row as needing a check against the page image.
4. **S06 and S09 are time-bound.** S06 covers Semester I only and S09 covers Semester II only; together they span the 2026/2027 session. Rows with the same number are near-identical in wording, so an answer must name the semester.
   S06 detail: As of 24 Nov 2026 several deadlines have passed (for example, special registration 28–29 Sep and amendment windows in October); withdrawal runs until 4 Dec 2026 and Semester II registration is 11–22 Jan 2027.
5. **S01 has no publication date.** Its `doc_date` is the PDF print date and the basis is stated in `doc_date_basis`.
6. **S02 edition label conflict** (16th Edition vs First Edition 2026) is recorded in the edition string.
7. **Lost layout in tables** (S01, S06, S09): the text is a flat stream. S06 and S09 rows and S01 blocks are rebuilt by splitters, but the column structure is not.
8. **Image content is partly supplemented** (S05 p9 legend, S08 Q6 and Q7). Other images (S05 p3, p8) are not indexed.
9. **Privacy.** S05's PDF Author field contains a staff member's name; it is never copied. S05 p3 shows a matric number; the page image is not stored or OCR'd. The privacy scan over the prepared text found 6 e-mail addresses (all UTM office or example addresses, such as `ugrad@utm.my`, `identitycard@utm.my`, `digitalcare@utm.my`, `utmid@utm.my`, `utmid@graduate.utm.my`, `yourutmid@graduate.utm.my`) and no phone numbers, 12-digit IDs or matric numbers.
10. **S02 is long (81 pages).** It is split by paragraph packing at 800 characters with `printed_page` and `section` kept for citations.

---

## Raw versus prepared storage

| Item | Location | Notes |
|---|---|---|
| Raw PDFs | `data/raw` (Colab runtime or private copy) | Not committed. |
| Prepared text and metadata | `data/processed/prepared_text_version1.3.jsonl` (260 records) | Full text of public documents; redistribution rights not checked, so commit it only if rights allow, otherwise commit a small sample. |
| Source register | This file, `data/week3_manifest_version1.3.json`, `data/pipeline_v1/documents/pipeline_manifest_version1.3.json` | Links each chunk to source, page and date. |

New metadata fields on every record in v1.1: `source_type`, `source_url`, `retrieved_date`, `doc_date_basis`, `valid_until`, `extraction_method`, `text_fixups`, plus `supplement_id`, `ocr_raw_text` and `ocr_token_recall` on the three OCR-supplemented records.
---

## Checkpoint A

- **Which Topic 2 requirements need this data?** FR1 and FR2, RAI2 (dates and validity), NFR4 (updating sources), and evaluation criterion 7 (time-sensitive and conflicting sources).
- **Loaded sample shown?** Yes: S06 row 10, S01 Semester I block, S08 Q6, S02 page 40 (see above; notebook Cells 4, 4b and 8A).
- **Pathway chosen:** A (PDF documents) with targeted OCR. Other pathways are not used because the project needs text documents.
