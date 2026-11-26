# Data Quality, Bias, and Responsible Data Report — UniGuide AI

**Evidence for every number is in `notebooks/week4_quality.ipynb`.**

## 1. Data Quality Scorecard

Rating scale: 5 = no findings; 4 = only warnings or minor items; 3 = open review items that can affect answers; 2 = a hard rule fails; 1 = unusable. Evidence for the five dimensions comes from notebook cell A2b, plus A4 (Pandera rules) and B1 (duplicates). The ratings are the author's judgement of that evidence.

| Dimension | Rating (1–5 / N.V.) | Evidence | Action / limitation |
|---|---:|---|---|
| Completeness | 4 | All citation and currency fields (`record_id`, `source_id`, `source`, `pdf_page`, `doc_date`, `currency_status`, `source_url`, `retrieved_date`) are present in 260 of 260 chunks. `section` is empty in 34 chunks (13.1%: S02 24, S05 10). Every required topic has at least one source. Image content is recovered in only 3 places (S05 p9 legend, S08 Q6, Q7). | Chunks without a section are cited by page. Other screenshots and infographics are not indexed, so questions that depend on them use the fallback. S05 pages 3 and 8 were left out on purpose (personal data). |
| Consistency | 3 | Two source conflicts: Federal Territory Day (S06 1 January 2027, S01 1 February 2027); approver for more than 18 credits (S02 Dean; S06 Academic Advisor and Deputy Dean). S06 prints "Janaury 2026" for Thaipusam. S09 (Semester II) keeps Semester I wording. 14 S02 chunks cite a Section number different from their header (mostly normal cross-references). The running header "Academic Regulations" at the start of 31 S02 chunks was removed (cell 5b; none remain). | Conflicts are kept, not deleted. Precedence rule (S02 decides rules; calendars and schedules decide dates); both sources are shown when a conflict cannot be resolved. S02 p36 header is to be compared with the page. |
| Accuracy | 4 | 257 pypdf chunks were compared with their own PDF page: minimum word recall 0.952; the 3 chunks below 0.98 are kerning repairs ("V ertical", "T otal", "T eknologi"). The author checked 11 chunks against the page images (5 random spot-check chunks, S08 Q6 and Q7, S02 Table 11, S05 p8 and p9): 7 correct, 4 corrected ("(step 9)", "FTF", two Table 11 notes). | Only 11 of 246 chunks were checked by eye; the rest rely on text recall, which cannot detect a value moved to the wrong row. Accuracy of the source PDFs themselves is not verified. |
| Timeliness | 3 | Every source has a document date, a basis and a currency label. No source is expired (S06 valid to 2027-03-21; S01 and S09 to 2027-10-03). The date is a proxy for S01, S05 and S09 (file name or PDF creation date; S09 prints only "September 2026"); the author confirmed that these three were published in 2026. S02 prints a year only ("First Edition 2026"). | The exact day of S01, S05 and S09 is not confirmed, so the assistant shows the year or the printed month and marks the day "not verified". After the valid-until dates the schedules are outdated. |
| Uniqueness | 4 | 0 exact duplicates (record IDs, SHA-256 of normalised text, source files). 7 near-duplicate pairs (5-word Jaccard 0.50–0.70) between S06 (Semester I) and S09 (Semester II). 11 of 22 chunks in each of S06 and S09 do not name the semester in their text. | The pairs are retained because the dates differ. The answer must name the semester (Topic 7). |

## 2. Validation and Data-Defect Findings

Rules R1–R8 were run with a Pandera lazy schema on the 260 Topic 3 chunks. In total there are 57 failure rows on 38 chunks (14.6%); see `week4_validation_failures.csv`.

| Rule | Quality concern | Observable condition | Failures (%) | Examples | Decision |
|---|---|---|---|---|---|
| R1 | Empty chunks give no evidence; no source means no citation (FR3, RAI1) | `text != ""` and `source != ""` | 0 (0%) | – | Hard fail |
| R2 | Garbled PDF extraction gives wrong answers | no NUL byte or U+FFFD | 0 (0%) | – | Hard fail |
| R3 | Page-number or heading fragments with no meaning | `char_count >= 50` (an illustrative warning level of about eight words, not a defect test) | 7 (2.7%) | `S02-p004-01`, `S02-p007-01`, `S02-p016-02`, `S02-p069-02`, `S02-p071-04`, `S05-p008-01`, `S09-p003-02` | Warning, then review each case |
| R4 | RAI2 needs a source date to show | `doc_date` and `currency_status` not empty | 0 (0%) | – | Hard fail |
| R5 | An expired time-bound source must not be treated as current | `valid_until` empty or not before the run date | 0 (0%) | – | Review |
| R6 | A chunk without a section gives only a page as locator | `section` not empty | 34 (13.1%): S02 24, S05 10 | all 10 S05 chunks (slide deck, no headings); S02 front matter | Review |
| R7 | Symbol-font characters (tick marks in a table) lose their meaning | no characters U+E000 to U+F8FF | 2 (0.8%) | `S02-p069-01`, `S02-p069-02` (Table 11) | Review |
| R8 | Cover, copyright, contents and contributor names do not answer questions and pollute retrieval | not S02 PDF page 12 or earlier | 14 (5.4%): all S02 | `S02-p004-01`, `S02-p006-01`, `S02-p012-01` | Review, then quarantine |

**Summary of cases found**
- **Missing:** 34 chunks without a section label.
- **Corrupted:** none by R2. Two chunks have lost tick marks (Table 11); after checking the page image, a note was added saying that "Still in studies" and "Mobility Program" are Counted and all other rows are Not Counted.
- **Noisy:** 14 front-matter chunks (cover, copyright, contents, list of about 44 contributor names) were quarantined; the running header was removed from 31 chunks.
- **Inconsistent:** the two source conflicts, the "Janaury" typo, and Semester I wording inside S09 (see Section 3).
- **Stale:** none expired. Past dates inside a schedule are normal and are not counted as a defect.
- **Uncertain:** the trailing "9" in `S05-p008-01` (confirmed as the slide's step number; now "(step 9)"), the "PTF" label in the S05 legend (confirmed as "FTF"), and the exact day of three document dates (still open).

R3 triage: of 7 short chunks, 2 are front matter (cover, contents header), 1 is the Table 11 fragment, and 4 are valid content that was kept (an abbreviation entry, the end of a MOOC sentence, a schedule row, and the S05 sentence with a step number). A hard fail on length would have deleted correct content.

## 3. Duplicate and Anomaly Findings

- **Exact duplicates:** 0 (SHA-256 on normalised text; duplicate record IDs: none; identical source files: none). Nothing was removed by deduplication.
- **Near-duplicates:** 7 S06/S09 pairs (`S06-p001-01`/`S09-p001-01`, p001-02, p001-06, p001-07, p002-02, p003-01, p003-04). Same row layout and wording, different dates. Decision: retain both (`week4_duplicate_review.csv`).
- **Outliers:** chunk length runs from 11 to 1,044 characters (median 502). The shortest are front matter or valid short rows (`S09-p003-02` has 45 characters: "Revision Period, 5-11 July 2027").
- **Contradictions:** Federal Territory Day (S06 1 January 2027 vs S01 1 February 2027); approver for more than 18 credits (S02 vs S06); S06 "22 Janaury 2026 Thaipusam" (spelling and year inside a 2026/2027 schedule); S09 (Semester II) rows that still say "January 2026" and "Semester I, 2026/2027". The "Submisssion" spelling in S06 row 9 is in the PDF itself and was kept.
- **Extraction problems:** kerning splits in 3 chunks (repaired by Topic 3); Table 11 tick marks lost; invigilator duty lists (`S02-p075-03`, `S02-p076-03`) that start in the middle of a list, so the role is only in the neighbouring chunk; `S02-p078-01` joins the end of Appendix 9 with the start of Appendix 10; S06 row 9 prints week and date columns together ("5 – 12 10 November 2026 - 1 January 2027").
- **Provenance preserved:** every chunk keeps `source`, `source_sha256`, `source_url`, `retrieved_date`, `pdf_page`, `doc_date` and `doc_date_basis`. Quarantined chunks stay in `prepared_chunks_quarantine.jsonl` with `quality_decision`. Changed chunks carry `manual_check`, `manual_fix` or `header_removed`; flagged chunks carry `quality_flag`.
- **Actions taken:** 14 chunks quarantined; running header removed from 31 chunks; 4 chunks corrected after checking page images; 22 chunks retained with a flag; no conflicting or near-duplicate content deleted.

## 4. Representation, Coverage, and Bias Assessment

**Required areas (reviewed corpus, 246 chunks)**

| Required area | Expected evidence | Actual evidence | Note |
|---|---|---|---|
| Academic calendar dates, both semesters | S01, S06, S09 | S01 5, S06 22, S09 22 chunks | Both semesters present |
| Registration deadlines and penalties | S06, S09 | S06, S09 | Present in both schedules |
| Academic rules (credit load, repeat, fees, exams) | S02 | S02 169 chunks | Large body of rule text; credit load appears only in S02 |
| Registration steps | S05 | S05 10 chunks | Slide deck; screenshots not indexed except the p9 legend. Least supported area (possible gap, to be tested in Topic 8) |
| Matric card services | S07 | S07 11 chunks | Q&A chunks |
| UTMID services | S08 | S08 7 chunks | Q6 and Q7 from images, checked by the author |

By source type: rules 169 chunks (68.7% of chunks, 82.1% of characters), dates 49 (19.9%, 11.0%), faq 18 (7.3%, 4.7%), how_to 10 (4.1%, 2.2%). `week4_coverage_by_source.png` shows the same. Unequal chunk counts are a signal for investigation, not evidence of unfairness; S01, S07 and S08 are small because the documents are short. Services outside the seven sources (health, hostel, financial aid, postgraduate rules) are out of scope by design.

**Bias risks (data-level risks, not proof of unfairness)**

| Bias type | Evidence | Propagation path |
|---|---|---|
| Historical | S02 Art. 42 applies "from the 2022/2023 academic session"; S09 keeps Semester I wording; S06 prints "Janaury 2026"; S02 and S06 name different approvers for more than 18 credits. The text was kept verbatim by Topic 3. | Source carries old or conflicting wording → both versions stay in the prepared corpus → a question about the approver or a date can retrieve either one → the answer may state one authority or an old date as current. |
| Representation | S02 is 68.7% of chunks and 82.1% of characters; S07 and S08 together are 7.3% of chunks. S02 covers full-time undergraduate programmes only. Source selection (a long regulation book) and paragraph packing both contribute. | Source and chunking decision → S02 dominates the prepared corpus → broad questions may retrieve S02 passages first → schedule rows or FAQ answers that fit the question may rank lower and not be shown. |
| Measurement | `doc_date` is a proxy for S01, S05 and S09 (S01: file name gives only the session; S05 and S09: PDF creation date; S09 prints "September 2026"); S02 prints a year only. | Proxy date chosen in Topic 3 because no day is printed → stored as the source date in the prepared corpus → retrieved with the chunk and shown in the citation → the student may believe the source is newer or more precisely dated than it is. |

Fairness performance of the whole system is not claimed here; it is evaluated in Topic 8.

## 5. Privacy, Consent, Rights/Access, and Data Governance

- **Ownership, version and provenance:** URL, retrieval date and SHA-256 are recorded for all 7 sources. Owners are named in the Week 3 inventory (UTM Academic Management Division, Student Affairs, UGS / UTM Press). Version and date basis: see Timeliness. **Gap:** exact day for S01, S05, S09.
- **Rights and access basis:** the inventory states "public UTM document; coursework use only". S02 is a UTM Press publication with a copyright notice ("All rights reserved"). Raw PDFs are not redistributed. **To verify:** permission or licence for S02. Until then, do not publish the full S02 text or the quarantine file in a public repository. "Publicly accessible" does not mean public domain.
- **Personal and sensitive data:** the Topic 3 corpus contained the list of contributors (about 44 staff names, S02 p12). It is quarantined; the reviewed corpus has none of these names. Six office or example e-mail addresses remain (`ugrad@utm.my`, `identitycard@utm.my`, `digitalcare@utm.my`, `utmid@utm.my`, `utmid@graduate.utm.my`, `yourutmid@graduate.utm.my`) because the assistant needs them to redirect students. No MyKad, matric or phone numbers were found. The matric number on S05 p3 and the S05 PDF author field were never copied; S05 pages 3 and 8 were not OCR'd.
- **Consent and collection basis:** N/A at present; the corpus is public institutional documents and no user data is collected. If user questions or chat logs are stored for evaluation (Topic 8), a privacy notice, consent and a retention rule must be added first.
- **Update and version-control owner:** `valid_until` is set for S01, S06 and S09; SHA-256 shows when a file changes. **Gap:** no named owner, review date or rule for superseded versions.
- **Unresolved governance gaps:** S02 rights; exact days of three dates; update owner and schedule.

## 6. Mitigation Plan and Residual Risk

| Issue | Evidence | Action | Owner / timing | Residual risk |
|---|---|---|---|---|
| Front matter and contributor names | R8: 14 chunks; names in `S02-p012-01/02` | Quarantined with reason; kept out of the reviewed corpus; keep the quarantine file out of public repositories | Student; done / before any commit | Low |
| Semester not named in S06/S09 rows | 7 near-duplicate pairs; 11 of 22 chunks in each source | Keep `source` in metadata; answers must name the semester | Student; Topic 7 prompt | Medium |
| Date and approver conflicts | Federal Territory Day; >18 credits approver | Precedence rule; show both sources when unresolved | Student; Topic 7 | Medium |
| Table 11 symbols, S05 step number, S05 legend | R7 2; R3 `S05-p008-01`; "PTF" | Checked against page images; note added to Table 11, "(step 9)", "FTF" | Student; done 2026-11-26 | Low |
| Running header, mixed Appendix 9/10 chunk | 31 S02 chunks; `S02-p078-01` | Header removed; mixed chunk is a warning only | Student; done | Low |
| Invigilator lists lose the role at chunk boundary | `S02-p075-03`, `S02-p076-03` | Retain; use overlap or neighbour retrieval in Topic 7 | Student; Topic 7 | Medium |
| Proxy dates | S01, S05, S09 basis | Show the year or printed month only; mark "day not verified" | Student; Topic 7 | Low–medium |
| Chunks without a section | R6: 34 | Cite by page; flag `R6_no_section_label` | Student; Topic 7 | Low |
| Other image content not indexed | 3 recovered places only | Fallback answer and redirect for image-dependent questions | Student; Topic 7 and 8 | Medium |
| S02 rights | inventory; copyright notice | Ask lecturer or UTM Press; do not publish the full text meanwhile | Student / lecturer; before public commit | Medium until answered |
| No update owner | no review rule | Write the refresh rule in the README: re-run Topics 3 and 4 when AMD posts a new schedule, before 21 Mar 2027 (S06) and 3 Oct 2027 (S01, S09) | Student; before Topic 7 | Low |

## 7. Impact on Topic 2 Requirements / Evaluation Criteria

| Requirement / threshold | What could fail if the issue stays unresolved |
|---|---|
| RAI2 (source dates shown 100%; past-deadline flag ≥ 95%) | Proxy dates (S01, S05, S09) and no update owner: the assistant could show a date that is not the true publication day or call an outdated schedule current after its valid-until date. |
| Criterion 7 (conflicting and time-sensitive sources) | Federal Territory Day and approver conflicts, and S06/S09 near-duplicates without a semester name, can give a wrong or mixed answer. |
| RAI1 (supported answers and correct fallback ≥ 95%) | Image-dependent questions (S05 screenshots, other infographics) have no source; the answer must fall back. Unverified accuracy outside the 11 checked chunks could give a wrong answer with a citation. |
| FR3 (cite sources) | 34 chunks have no section label, so the citation locator is the page only. |
| NFR1 / NFR3 (correctness ≥ 85%, Recall@5 ≥ 80%) | S02 makes up 68.7% of the chunks and 82.1% of the text, so retrieval may rank S02 first and miss S06/S09 or FAQ chunks; chunks that lose context at a boundary (invigilator lists) can be retrieved without their role. |
| RAI3 (paraphrase correctness ≥ 80%, gap ≤ 10 points) | Exact-match terms such as "Janaury" and wording that differs between S02 and S06 can make some paraphrases fail. |
| NFR4 (document update ≤ 10 min) and rights | No update owner or rule; unresolved S02 rights could block publication of the project data. |

## 8. Topic 4 Quality Gate Decision

**GO WITH CONDITIONS.** The hard rules R1, R2 and R4 have 0 failures, there are no exact duplicates, no source has expired, and the 14 front-matter and personal-name chunks are out of the reviewed corpus. Topic 7 proceeds with `chunks_clean.pkl` / `prepared_chunks_clean.jsonl`, not with the Topic 3 files, under these conditions:

| # | Condition | Owner | Follow-up evidence |
|---|---|---|---|
| 1 | Confirm S02 rights (lecturer or UTM Press); keep the full S02 text and the quarantine file out of public repositories until then | Student / lecturer | Written permission or licence note in the README |
| 2 | Topic 7 names the semester and the source date in every schedule answer, shows the year or printed month where the day is not printed, and applies the precedence rule or shows both sources when a conflict is open | Student | Topic 8 conflict and stale-date tests (target ≥ 90% correct handling) |
| 3 | Write the update rule and owner in the README (re-run Topics 3 and 4 before 21 Mar 2027 and 3 Oct 2027) | Student | README section |
| 4 | Test image-dependent and step-type questions (S05) and show the fallback when no source exists | Student | Topic 8 questions on registration steps |
| 5 | Compare S02 p36 section header and any new image content with the page before adding it to the corpus | Student | Page check note |

Items already completed on 2026-11-26: checks of 11 chunks against the page images (4 corrected); confirmation that S01, S05 and S09 were published in 2026.
