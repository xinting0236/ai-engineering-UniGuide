# Week 3 — Data Inventory — UniGuide AI

**Project:** UniGuide AI — AI-Powered University FAQ Assistant
**Pathway used:** A — Documents (PDF). This is the real project dataset, not the synthetic practice pack.
**Download date for all sources:** 10 November 2026

---

## A1 — Requirements → data needs

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| FR1 / FR2 | Academic calendar dates, course registration procedures, academic regulations, student services information | S01 calendar, S02 regulations, S03 handbook, S04 and S05 registration manuals | Official UTM publications covering all four scope areas. Constraint: S03 is dated 2010, and the S04 and S05 manuals contain image-only content. |
| RAI2 | Source publication date, edition, version and retrieval date | PDF metadata (creation date), edition statements inside the documents, my download date | Dates exist for S01, S02 and S03 and are partial for S04 and S05, so some sources will need the "currency not verified" flag. |
| NFR4 | A repeatable way to re-ingest an updated document | Same file names with a version or date recorded in this inventory and in the metadata manifest | Pipeline must reprocess one file without rebuilding the rest. |

---

## A4 — Data inventory

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `utm_academic_calendar_2026_2027.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/05/Final_Kalendar-Akademik-Sarjana-Muda.Sarjana.Kedoktoran-20262027-14.5.2026-pembetulan.pdf | PDF, table-based calendar (English page + Malay weekly breakdown). Supports FR1/FR2 (academic calendar). | **Academic Calendar 2026/2027**, Bachelor, Master and Doctoral Programme. PDF created 12 May 2026, modified 13 May 2026. The URL filename ends in "14.5.2026-pembetulan" (Malay for "correction"), so this is a corrected version dated 14 May 2026. Owner: Universiti Teknologi Malaysia (UTM). | Public official UTM document. Used for coursework only; raw file not redistributed. | 2 pages, 8,335 characters. Text layer present on both pages. Page 1 is in English and page 2 is in Malay. Layout loss and errors are listed below. |
| S02 | `utm_academic_regulations.pdf` — https://ugs.utm.my/wp-content/uploads/sites/8/2026/05/FT-UG-ACADEMIC-REGULATIONS-16th-edition.pdf | PDF book, undergraduate academic regulations. Supports FR1/FR2 (academic regulations, registration rules). | **Academic Regulations, Full-Time Undergraduate Studies Programme, 16th Edition, 2026.** Editors: Mohd Shafry Mohd Rahim and Intan Zaurah Mat Darus; co-editor Mahyuddin Arsat. Publisher: Penerbit UTM. PDF created 7 May 2026. | Public official UTM publication, copyright reserved. Used for coursework only; raw file not redistributed. | 81 pages, 105,131 characters; 75 `/gid` glyph codes (all on the title page). Text layer present. Pages with almost no text: 1, 2, 3, 11, 13, 15, 81 (expected to be cover and divider pages; confirm). The title on page 4 extracts as `/gid…` glyph codes. |
| S03 | `utm_handbook.pdf` — https://www2.utm.my/wp-content/uploads/sites/8/2011/01/buku_acad.advising170810_a4.pdf | PDF booklet, *Academic Advising: Ensuring Student Success*. Supports FR1/FR2 (registration procedure, student services contacts). | **2010 edition**, Office of Undergraduate Studies, UTM. PDF created 17 August 2010 (the URL path shows it was uploaded in January 2011). | Public UTM publication, all rights reserved. Used for coursework only; raw file not redistributed. | 28 pages, 39,859 characters. Text layer present. Pages with almost no text: 1 (cover) and 28 (back cover). The page 5 registration flowchart is an image. |
| S04 | `utm_User-Manual_NewStudent_Course-Registration_update-202620271.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/10/User-Manual_NewStudent_Course-Registration_update-202620271.pdf | PDF slide deck, step-by-step course registration for new full-time bachelor's students. Supports FR1/FR2 (course registration). | Filename says "update 2026/2027". PDF created 5 October 2026, modified 6 October 2026. Owner: Academic Management Division (AMD), UTM. Screenshots show session 202420251 and 2024 dates. | Public UTM document. Used for coursework only; raw file not redistributed. | 24 pages, 3,752 characters. Only slide text and captions were extracted; screenshots were not. Pages with almost no text: 1, 2, 5 (title, roadmap and image-heavy slides). |
| S05 | `utm_User-Manual_Course-Registration-Senior-Student_update202620271.pdf` — https://amd.utm.my/wp-content/uploads/sites/460/2026/10/User-Manual_Course-Registration-Senior-Student_update202620271.pdf | PDF slide deck, step-by-step course registration for full-time bachelor's senior students. Supports FR1/FR2 (course registration). | Filename says "update 2026/2027". PDF created 6 October 2026. Owner: Academic Management Division (AMD), UTM. Screenshots show session 202420252; an example slip is dated 3 Jan 2026. | Public UTM document. Used for coursework only; raw file not redistributed. | 10 pages, 2,048 characters. Every page has some text, but tables and the registration slip are image-only. |

---

## Inspection evidence (two representative sources)

**Source 1: S01 academic calendar, page 1 (sample from the extracted text layer)**
- Extracted text begins with the table header "DATE PROGRAMME PUBLIC HOLIDAY", followed by the new-student registration dates for Semester I 2026/2027.
- Key rows are readable: Semester I runs 12 October 2026 – 21 March 2027 (23 weeks) and Semester II runs 22 March 2027 – 3 October 2027 (28 weeks).
- Limitation: it is a three-column table, and the extractor returns date, programme and holiday text as a flat stream. A retrieved chunk may therefore mix a date with the wrong holiday unless the rows are rebuilt.

**Source 2: S02 academic regulations, page 20 (sample from the extracted text layer)**
- Extracted text is clean prose: running header "Academic Regulations Section 1" followed by definitions such as "Graduation Credits", "Earned Credits" and "Calculated Credits".
- Page 40 (Section 4) reads clearly and includes numbered clauses, for example about credit transfer for mobility students, so clause numbers can be kept for citations.
- Colab confirmed: PDF page 20 prints as page 3 and PDF page 40 as page 23 (offset of 17).
- Limitation: page 4 (the title page) extracts as glyph codes rather than the title. The rendered page shows "Academic Regulations, Full-Time Undergraduate Studies Programme, 16th Edition", so edition metadata must be entered manually.

---

## Ingestion limitations observed

1. **S01 contains conflicting date errors in the Malay page.** Examples: "7 Disember 2026 – 24 Januari 2026" (year should be 2027), "4 – 8 Jan 202" (truncated year), "19 – 14 Feb 2027" (impossible range), "1 Mei 2026 (Sabtu)" and "31 Ogos 2026" (the year looks wrong for the 2026/2027 session), and "Minggu 19" appearing twice. The English page 1 and the Malay page 2 can disagree. Decision for Part B: treat page 1 (English) as the primary source and flag page 2 as lower confidence.
2. **S01 is bilingual.** The same facts appear in English and Malay, so retrieval may return duplicates. Keep a `language` field in metadata.
3. **S03 is a 2010 booklet.** It describes AIMS 2000 and aimsweb.utm.my (systems the newer manuals replace with MyUTM Portal) and an older registration procedure. It should be labelled "currency not verified", or excluded from authoritative answers, when it conflicts with newer sources.
4. **S04 and S05 are slide decks with mostly image content.** Roughly 2,000 to 3,800 characters were extracted from 10 to 24 slides. Course tables, the registration slip and screenshot captions are images, so questions about them cannot be answered from extracted text unless OCR is added.
5. **S04 and S05 carry old session labels.** The screenshots show 2024 session codes and dates even though the files are labelled as 2026/2027 updates.
6. **Privacy:** S05's PDF metadata contains a staff member's name in the Author field, and some S04 screenshots show a student name and ID numbers. Do not copy the Author field into the prepared metadata, and do not store or publish the page images.
7. **S02 is long (81 pages).** It needs chunking by section or clause rather than by page, so citations can point to a clause.
8. **S02 PDF page numbers differ from printed page numbers.** In the Colab run, PDF page 20 shows printed page "3" (Section 1) and PDF page 40 shows printed page "23" (Section 4), an offset of 17. The prepared metadata must store both `pdf_page` and `printed_page` (plus section and clause number) so a citation matches what students see in the book.
9. **Extracted text has irregular spacing** (for example doubled spaces such as "of  national" and trailing spaces in the calendar). Whitespace will be normalised in Part B.

---

## Raw versus prepared storage

| Item | Location | Notes |
|---|---|---|
| Raw PDFs | `/content/week3_project/data/raw` (temporary Colab runtime) | Not committed to public GitHub. Keep a copy in a private location. |
| Prepared text and metadata | `/content/week3_project/data/processed` (Part B output) | Exported and committed only if redistribution is allowed; otherwise commit the pipeline and a small sample. |
| Source register | This file plus a metadata manifest created in Part B | Links each prepared chunk back to its source ID, page and date. |

---

## Checkpoint A

- **Which Topic 2 requirements need this data?** FR1 and FR2 (answering from official UTM documents), RAI2 (dates and versions), and NFR4 (updating sources).
- **Loaded sample shown?** Yes: real extracted text from S01 (page 1) and S02 (pages 20 and 40), described above. Colab Cell 4 and Cell 4b output confirm this. 🟥 Save screenshots of both outputs to `docs/evidence/` in your repository.
- **Pathway chosen:** A (PDF documents). The other five pathways are not used because the project only needs text documents.
