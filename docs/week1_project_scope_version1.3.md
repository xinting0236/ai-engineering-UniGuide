 **Version 1.3 changes**: *Problem Statement*, *Evidence 3*, *Target Users*, *Data Sources*, *Different from EduAdvisor*, *Risk 1 and Risk 2 mitigations*. 

## Project Name

**UniGuide AI — AI-Powered University FAQ Assistant**

## Domain

Higher Education / Student Services / Natural Language Processing / Retrieval-Augmented Generation (RAG)

## Problem Statement

University students often need to find information about academic dates, course registration, academic regulations, and matric card or UTMID services. This information is published in separate official documents (rules, calendars, work schedules, step-by-step manuals and FAQs), and some of it is time-bound, so it can be time-consuming to find the correct and current answer.

UniGuide AI will use semantic retrieval and a large language model to answer students' questions using official 2026 UTM documents. It will provide source citations and source dates so students can verify the information, and will indicate when the available documents do not contain a sufficient answer.


## Evidence of the Problem

**Evidence 1:** UTM publishes course registration requirements and procedures through its Academic Management Division website. Students must understand the applicable registration rules and deadlines, making accurate access to this information important.

**Source / citation:** [UTM Academic Management Division — Course Registration Guidelines](https://amd.utm.my/course-registration/)

**Evidence 2:** UTM's Faculty of Computing Student Development Hub provides separate resources for academic calendars, academic regulations, course registration timetables, and undergraduate handbooks. This demonstrates that relevant student information is available through multiple resources rather than one single FAQ page.

**Source / citation:** [UTM Faculty of Computing — Student Development Hub](https://comp.utm.my/student/home/)

## Initial Related Work (at least 3)

### Study / Solution 1: Academic FAQ Chatbot Using Retrieval-Augmented Generation

**Description:** This study develops an academic FAQ chatbot using document preprocessing, embedding-based retrieval, a vector database, and a large language model to answer questions about academic information.

**Source / citation:** [Academic FAQ Chatbot Using Retrieval Augmented Generation (2026)](https://ejournal1.unud.ac.id/index.php/jitter/article/view/4289)

**Key limitation:** A chatbot developed for a particular academic information collection may not directly support UTM's own documents, policies, and student information requirements. Its answers must also be evaluated against the relevant official sources.

### Study / Solution 2: Retrieval-Augmented Generation for Educational Applications — A Systematic Survey

**Description:** This survey reviews RAG applications in education and discusses technical components such as indexing, retrieval, and answer generation.

**Source / citation:** [Computers and Education: Artificial Intelligence (2025)](https://www.sciencedirect.com/science/article/pii/S2666920X25000578)

**Key limitation:** The survey identifies challenges such as hallucinations, outdated knowledge, and limited multimodal capabilities. A general RAG framework does not automatically guarantee correct answers or reliable citations for university information.

### Study / Solution 3: Label-Free Evaluation of Retrieval-Augmented Generation in a University Policy Chatbot

**Description:** This study evaluates a RAG chatbot for university policy documents, examining answer relevance, groundedness, and retrieval effectiveness.

**Source / citation:** [ACM Technical Symposium on Computer Science Education (2026)](https://doi.org/10.1145/3770761.3777161)

**Key limitation:** The study reports that answer relevance and groundedness remain imperfect, demonstrating that a university chatbot needs systematic evaluation rather than assuming that retrieved documents always produce correct answers.

## Engineering Gap / Limitation

Existing university information sources provide authoritative content, while RAG-based chatbots offer a more conversational way to access it. However, students may still receive incomplete, outdated, or unsupported answers if retrieval and answer generation are unreliable.

UniGuide AI addresses this practical gap by integrating semantic document retrieval, source-grounded answer generation, citations, and an insufficient-evidence fallback into one web application using official UTM information. Its performance will be measured using a prepared set of questions with expected answers and supporting document passages.

## Initial Project Objectives (2–3)

**O1:** To collect and prepare official 2026 UTM documents on the academic calendar, academic regulations, course registration, and matric card / UTMID services for the knowledge base, keeping source, date and currency metadata for every passage.

**O2:** To develop a web-based university FAQ assistant using semantic retrieval and a large language model to generate document-grounded answers with source citations and appropriate fallback behaviour.

**O3:** To evaluate the system using answer correctness, retrieval relevance, citation accuracy, unsupported-answer handling, and response latency.

## Target Users

UTM full-time bachelor's degree students, both new and continuing, who need information about academic dates, course registration, academic regulations, and matric card / UTMID services.

The initial version will focus on publicly accessible information and will not provide personalised academic decisions or access to private student records.

## Data Sources (at least 2)

| ID | Source | Purpose | URL |
|---|---|---|---|
| S01 | Academic Calendar 2026/2027 (English) | Semester, break, examination and public-holiday dates | https://amd.utm.my/wp-content/uploads/sites/460/2026/05/Final_Kalendar-Akademik-Sarjana-Muda.Sarjana.Kedoktoran-20262027-14.5.2026-pembetulan.pdf |
| S02 | Academic Regulations, Full-Time Undergraduate Studies Programme | Authoritative rules: credit load, repeating courses, fees, examinations | https://ugs.utm.my/wp-content/uploads/sites/8/2026/05/FT-UG-ACADEMIC-REGULATIONS-16th-edition.pdf |
| S05 | Step-by-step Course Registration — Senior Student (update 2026/2027) | Registration steps and the slip legend | https://amd.utm.my/wp-content/uploads/sites/460/2026/10/User-Manual_Course-Registration-Senior-Student_update202620271.pdf |
| S06 | UG Work Schedule for Course Registration, Award of Degree, Examination — Semester I 2026/2027 | Dates, penalties and approval routes for Semester I | https://amd.utm.my/wp-content/uploads/sites/460/2026/10/Student-202620271-UG-Work-Schedule-for-Course-Registration.Award-of-Degree.Examination-1.pdf |
| S09 | UG Work Schedule for Course Registration, Award of Degree, Examination — Semester II 2026/2027 | Dates, penalties and approval routes for Semester II | https://amd.utm.my/wp-content/uploads/sites/460/2026/09/Student-202620272-UG-Work-Schedule-for-Course-Registration.Award-of-Degree.Examination.pdf |
| S07 | FAQ Matric Card (updated 1 Oct 2026) | Matric card photo, replacement and contact answers | https://studentaffairs.utm.my/wp-content/uploads/2026/10/FAQs-Kad-Matrik-2026.pdf |
| S08 | FAQ UTMID (updated 29 Sep 2026) | UTMID, official e-mail, password reset and UTMSmart answers | https://studentaffairs.utm.my/wp-content/uploads/2026/09/UTMID-2026.pdf |

*Retired from version 1.0:* S03 (2010 advising handbook) and S04 (new-student manual).

## Similar to EduAdvisor

Both UniGuide AI and EduAdvisor are document-grounded question-answering systems designed to help students access university information more efficiently. Both can use official documents, semantic retrieval, a large language model, and source citations to produce answers that users can verify.

## Different from EduAdvisor

UniGuide AI focuses on general university FAQs: academic calendar dates, course registration rules and procedures, academic regulations, and matric card / UTMID services. EduAdvisor focuses more specifically on academic advising, including course prerequisites, course planning, and graduation requirements.

UniGuide AI will initially prioritise finding and explaining information in official UTM resources rather than making personalised academic recommendations.

## Responsible AI Risks (at least 2)

**Risk 1 — Hallucination and incorrect information:** The assistant may generate incorrect registration requirements, dates, or policies. This could mislead students.

**Mitigation:** Retrieve relevant official passages, provide source citations, evaluate answers against verified reference answers, and decline to answer when the documents do not provide sufficient evidence. When two sources disagree (for example Dean vs Deputy Dean approval for more than 18 credits), apply the precedence rule: regulations (S02) for rules, calendar (S01) and work schedule (S06) for dates, manuals (S05) and FAQs (S07, S08) for steps and services; show both sources when the conflict is unresolved.

**Risk 2 — Outdated information:** Academic calendars, registration dates, and university policies may change. Answers based on old documents could mislead students.

**Mitigation:**  Record document versions and update dates, prioritise current official sources, and display the source date when available. Flag information that has not been verified as current. Tag time-bound sources (S06 covers Semester I 2026/2027 only) with `valid_until`, and tell the student when a deadline in the retrieved passage is already past.

**Risk 3 — Unequal answer quality:** Some student questions may be answered less accurately because of ambiguous wording, unfamiliar terminology, or insufficient coverage in the knowledge base.

**Mitigation:** Test the assistant with differently phrased questions, identify gaps in retrieval, use clear fallback messages, and direct students to the relevant official university source when necessary.
