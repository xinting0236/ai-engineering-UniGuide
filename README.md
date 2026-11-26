# UniGuide AI — AI-Powered University FAQ Assistant

> Answers UTM full-time bachelor's students' questions about the academic calendar, academic regulations, course registration and matric card / UTMID services, using official 2026 university documents.

*Version 1.3 — see [`docs/change_log.md`](docs/change_log.md) for what changed since version 1.0.*

## Problem Statement

UTM students may need to consult multiple university webpages and documents to find information about academic calendars, course registration, academic regulations, and student services. Although official information is available, it is distributed across different sources, making it less convenient to locate relevant answers. UniGuide AI uses retrieval-augmented generation (RAG) to provide answers grounded in official UTM documents, with source citations and a fallback when sufficient evidence is unavailable.

## Evidence of the Problem

- UTM's Academic Management Division publishes course registration guidelines and procedures on its official website.  
  Source: https://amd.utm.my/course-registration/
- UTM's Faculty of Computing Student Development Hub provides links to student resources, including academic calendars, academic regulations, and course registration information.  
  Source: https://comp.utm.my/student/home/
  - The 2026 documents themselves show the split: the rules (regulations), the dates (calendar, work schedule), the steps (registration manual) and the service answers (matric card and UTMID FAQs) are in seven separate PDFs.

## Initial Related Work / Existing Approaches

- **Academic FAQ Chatbot Using Retrieval Augmented Generation (2026)** — Uses retrieval-augmented generation for academic question answering. A similar approach would need to be adapted to official UTM documents and evaluated for answer accuracy.  
  Source: https://ejournal1.unud.ac.id/index.php/jitter/article/view/4289
- **Retrieval-Augmented Generation for Educational Application: A Systematic Survey (2025)** — Reviews RAG applications in education and discusses limitations such as hallucinations and outdated information. UniGuide AI will address these concerns through source-grounded answers and an insufficient-evidence fallback.  
  Source: https://www.sciencedirect.com/science/article/pii/S2666920X25000578
- **Label-Free Evaluation of Retrieval-Augmented Generation in a University Policy Chatbot (2026)** — Examines the evaluation of a university policy chatbot, including relevance, groundedness, and retrieval effectiveness. UniGuide AI will evaluate these aspects using UTM-related questions and official documents.  
  Source: https://doi.org/10.1145/3770761.3777161

## Engineering Gap / Limitation

Existing approaches demonstrate the potential of document-grounded chatbots, but UniGuide AI focuses on integrating official UTM information into one web-based assistant. The system will combine semantic retrieval, language-model-generated answers, source citations, and an insufficient-evidence fallback. Its performance will be evaluated to determine whether answers are relevant, accurate, and supported by the retrieved documents.

## Project Objectives

- **O1:** To collect and prepare official 2026 UTM documents on the academic calendar, academic regulations, course registration and matric card / UTMID services for the knowledge base, keeping source, date and currency metadata for every passage.
- **O2:** To develop a web-based AI FAQ assistant using retrieval-augmented generation that provides source-grounded answers with citations and an insufficient-evidence fallback.
- **O3:** To evaluate the system based on answer correctness, retrieval relevance, citation accuracy, unsupported-answer handling, and response latency.

## Target Users

UTM full-time bachelor's degree students (new and continuing) who need to find information about academic dates, course registration, academic regulations, and matric card / UTMID services.

## Data Sources

| ID | Document | Role | Date / version |
|---|---|---|---|
| S01 | Academic Calendar 2026/2027 (English, 1 page) | Dates | PDF printed 20 Nov 2026; no publication date on the page |
| S02 | Academic Regulations, Full-Time UG (16th Edition on title page; "First Edition 2026" on copyright page) | Rules | 2026 |
| S05 | Step-by-step Course Registration, Senior Student (update 2026/2027) | Steps | Created 6 Oct 2026; currency verified by the student |
| S06 | UG Work Schedule for Course Registration, Award of Degree, Examination (Semester I 2026/2027) | Dates, penalties | 9 Oct 2026; time-bound |
| S09 | UG Work Schedule for Course Registration, Award of Degree, Examination (Semester II 2026/2027) | Dates, penalties | Sep 2026 (PDF created 10 Sep 2026); time-bound |
| S07 | FAQ Matric Card | Service answers | Updated 1 Oct 2026 |
| S08 | FAQ UTMID | Service answers | Updated 29 Sep 2026 |

Retired from version 1.0: S03 (2010 advising handbook, quarantined as outdated) and S04 (new-student manual, dropped as image-heavy and overlapping S05).
Document URLs for S01, S02, S05–S09 are recorded in the data inventory.


## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | System map + project scope | `docs/week1_observation_sheet.md`, `docs/week1_system_map.png`, `docs/week1_project_scope_version1.3.md` | ✅ (scope updated in v1.3) |
| W2 | Requirements, evaluation criteria, architecture | `docs/week2_requirements_version1.3.md`, `docs/week2_evaluation_criteria_version1.1.md`, `docs/week2_architecture_decisions_version1.3.md`, `docs/week2_architecture_v1.png` | ✅ (updated in v1.1) |
| W3 | Data inventory + prepared data pipeline | `docs/week3_data_inventory_version1.3.md`, `docs/week3_pipeline_decisions_version1.3.md`, `docs/week3_pipeline_diagram_version1.3.png`, `notebooks/week3_pipeline_version1.3.ipynb`, manifests | ✅ (rebuilt on the new dataset) |
W4 | Data quality, bias and responsible data | `notebooks/week4_quality.ipynb`, `data/chunks_clean.pkl`, `data/prepared_chunks_clean.jsonl`, `docs/week4_data_quality_report.md`, `docs/week4_validation_failures.csv`, `docs/week4_duplicate_review.csv`, `docs/week4_coverage_by_source.png` | ✅ GO WITH CONDITIONS |
| — | Change log | [`docs/change_log.md`](docs/change_log.md) | ✅ |


## Data used by later topics (Topic 4 quality gate)

**Topic 7 (indexing and retrieval) must load the reviewed corpus:** 
data/chunks_clean.pkl (246 LangChain Document objects) or data/prepared_chunks_clean.jsonl. Do not use chunks.pkl or prepared_chunks.jsonl (260 chunks, they still contain front matter and staff names).

- The 14 chunks removed from the Topic 3 output (S02 cover, copyright, contents and contributor names) are kept for review in data/prepared_chunks_quarantine.jsonl. This file contains staff names and is not committed to the repository.
- 22 retained chunks carry a quality_flag in their metadata; 11 chunks carry manual_check (checked against the PDF page images).
- Quality gate decision: GO WITH CONDITIONS (see docs/week4_data_quality_report.md, Section 8). Conditions for Topic 7: name the semester and show the source date in every schedule answer; apply the source precedence rule or show both sources when they conflict; show the year or printed month where the exact day is not printed; use the fallback for questions that depend on screenshots that are not indexed.
- Refresh rule: re-run the Topic 3 pipeline and the Topic 4 notebook when AMD publishes a new schedule, before 21 Mar 2027 (S06) and before 3 Oct 2027 (S01, S09).
- Rights: S02 is a UTM Press publication with a copyright notice; permission is to be confirmed before the full S02 text is published.


## Repository Structure

```
README.md
docs/        week1_*, week2_*, week3_*  (*_version1.3.*), week4_data_quality_report.md,
             week4_validation_failures.csv, week4_duplicate_review.csv, week4_coverage_by_source.png
notebooks/   week3_pipeline_version1.3.ipynb, week4_quality.ipynb
data/        week3_manifest_version1.3.json, processed/prepared_text_version1.3.jsonl,
             pipeline_v1/documents/pipeline_manifest_version1.3.json, prepared_text_version1.3.jsonl,
             chunks_clean.pkl, prepared_chunks_clean.jsonl (Topic 4 reviewed corpus, 246 chunks)
```