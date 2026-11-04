# Section 0 — Project Framing & Application Scope Checkpoint

## 0.1 Problem Evidence

### Evidence 1: UTM Academic Management Division — Course Registration Guidelines

**Source:** https://amd.utm.my/course-registration/

UTM publishes course registration requirements and procedures through its Academic Management Division website. Students need to understand registration rules, procedures, and deadlines. This demonstrates the importance of providing accurate and accessible academic information.

### Evidence 2: UTM Faculty of Computing — Student Development Hub

**Source:** https://comp.utm.my/student/home/

The Student Development Hub provides different student resources, including academic calendars, academic regulations, course registration timetables, and undergraduate handbooks. These resources are distributed across multiple webpages and documents, making it less convenient for students to locate specific information.

## 0.2 Existing Approaches

| No. | Study / Solution | What It Addresses |
|---|---|---|
| 1 | Academic FAQ Chatbot Using Retrieval-Augmented Generation (2026) (AB Ariwibowo. (2026). Academic FAQ Chatbot using Retrieval Augmented Generation. Academia.eduAB AriwibowoJITTER, 2026•academia.Edu. https://www.academia.edu/download/133746829/Academic_FAQ_Chatbot_using_Retrieval_Augmented_Generation.pdf) | Uses document preprocessing, semantic retrieval, a vector database, and an LLM to answer academic questions. |
| 2 | Retrieval-Augmented Generation for Educational Applications — A Systematic Survey (2025) (Li, Z., Wang, Z., Wang, W., Hung, K., Xie, H., & Wang, F. L. (2025). Retrieval-augmented generation for educational application: A systematic survey. ElsevierZ Li, Z Wang, W Wang, K Hung, H Xie, FL WangComputers and Education: Artificial Intelligence, 2025•Elsevier, 8. https://doi.org/10.1016/J.CAEAI.2025.100417) | Reviews RAG applications in education, including document indexing, retrieval, and answer generation. |
| 3 | Label-Free Evaluation of Retrieval-Augmented Generation in a University Policy Chatbot (2026) (L Hayrapetyan. (2026). Label-free evaluation of retrieval-augmented generation in a university policy chatbot. Dl.Acm.orgL HayrapetyanProceedings of the 57th ACM Technical Symposium on Computer Science, 2026•dl.Acm.Org, 1665–1666. https://doi.org/10.1145/3770761.3777161) | Evaluates university policy chatbot performance through answer relevance, groundedness, and retrieval effectiveness. |

## 0.3 Project / Engineering Gap

Although existing university resources provide authoritative information and RAG-based chatbots offer conversational access, students may still receive incomplete, outdated, or unsupported answers due to unreliable retrieval and answer generation.

UniGuide AI addresses this practical limitation by integrating semantic document retrieval, source-grounded answer generation, source citations, and insufficient-evidence fallback behaviour into one web application using official UTM information.

## 0.4 Project Objectives

- **O1:** To collect and prepare official UTM documents and webpages covering academic calendars, course registration, academic regulations, and student services for the knowledge base.
- **O2:** To develop a web-based university FAQ assistant using semantic retrieval and a large language model to generate document-grounded answers with source citations and appropriate fallback behaviour.
- **O3:** To evaluate the system using answer correctness, retrieval relevance, citation accuracy, unsupported-answer handling, and response latency.

## 0.5 Application Scope

| Framing Element | Description |
|---|---|
| Intended Users | UTM undergraduate students, particularly new students and students seeking academic and student service information. |
| Supported Tasks | Answer questions about academic calendars, course registration procedures, academic regulations, and student services. Provide relevant source citations and indicate when sufficient information is unavailable. |
| Information / Knowledge Domain | Official, publicly accessible UTM academic and student service documents and webpages. |
| Operating Context | A web-based application that students can access through a browser to ask questions in natural language and receive answers based on the prepared UTM knowledge base. |
| Important Constraints | Limited to publicly accessible official UTM information. Document accuracy and update frequency depend on available university sources. The project does not access private student records. |
| Explicit Out-of-Scope Tasks | Personalised academic advising, individual course selection, graduation eligibility decisions, prerequisite waiver decisions, private student record enquiries, and administrative transactions. |

## 0.6 Evaluation Focus

The evaluation will determine whether UniGuide AI can retrieve relevant information from official UTM documents, generate correct and grounded answers, provide accurate source citations, identify questions that cannot be sufficiently answered, and maintain acceptable response latency.

---

# Section 1 — Functional Requirements (FR)

# Requirements Specification - UniGuide

Version: 1.0  

| ID | Requirement — The System SHALL | Scope Boundary — SHALL NOT |
|---|---|---|
| FR1 | Answer student questions about academic calendars, course registration procedures, academic regulations, and student services using the prepared UTM knowledge base. |Invent dates, procedures, or regulations that are not stated in the official UTM documents. |
| FR2 | Retrieve relevant information from official UTM documents and webpages based on the meaning and context of students' natural-language questions. | Retrieve or rely on unrelated or unverified information as authoritative UTM information. |
| FR3 | Generate answers based on the retrieved document passages and provide source citations so students can verify the information. | Present unsupported information as if it came from an official UTM document. |
| FR4 | Identify questions for which the knowledge base does not contain sufficient supporting information and provide an appropriate fallback response. | Guess or generate answers when sufficient supporting evidence is unavailable. |
| FR5 | Present answers in plain, clear language appropriate for UTM undergraduate students, avoiding unexplained technical or administrative jargon. |  |
| FR6 | Decline questions outside the defined application scope (personalized academic advising, private student records, administrative transactions) and redirect the student to the appropriate UTM office or official webpage. | Make personalized academic decisions (e.g. course selection, graduation eligibility, prerequisite waivers) or perform university administrative transactions. |

---

# Section 2 — Non-Functional Requirements (NFR)

| ID | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Correctness | The system shall generate answers that are factually consistent with the official UTM source documents. | Evaluate a prepared dataset of questions against verified reference answers. | Answer correctness rate | ≥ 85% |
| NFR2 | Performance | The system shall provide responses within an acceptable time for interactive student enquiries. | Measure end-to-end response time across a test set of questions. | 90th percentile response latency (p90) | ≤ 5 seconds |
| NFR3 | Retrieval Reliability | The system shall surface the relevant supporting passage(s) for a student's question among its top-ranked retrieved results. | Compare retrieved passages against manually identified relevant supporting passages. | Recall@5 | ≥ 80% |
| NFR4 | Maintainability | The system shall allow updated official documents to be incorporated into the knowledge base when university information changes. | Perform a document update test and measure the time required to reflect the changes. | Document update time | ≤ 10 minutes per document |

---

# Section 3 — Responsible AI Requirements (RAI)

| ID | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Hallucination and Incorrect Information | The system shall provide answers supported by retrieved official UTM documents and use a fallback response when sufficient evidence is unavailable. | Test with answerable and unanswerable questions and manually verify answers against source documents. | Supported-answer rate for answerable questions; correct fallback rate for unanswerable questions. | ≥ 95% supported answers; ≥ 95% correct fallback |
| RAI2 | Outdated Information | The system shall display available source dates or document versions and explicitly mark sources without date/version information as "currency not verified". | Test documents with different publication or update dates, including some with no date metadata, and inspect the displayed source information. | (a) Source-date/version display rate when metadata is available; (b) "currency not verified" flag rate for sources lacking metadata | (a) 100%; (b) 100% |
| RAI3 | Unequal Answer Quality (Robustness to Phrasing) | The system shall provide consistent answer quality for questions with different wording and levels of clarity. | Test equivalent questions using different phrasings and compare the correctness of their answers. | Answer correctness rate across paraphrased question sets; gap versus standard-phrased questions | ≥ 80% and gap ≤ 10 percentage points |
| RAI4 | Safety / Scope | The system shall refuse out-of-scope queries and redirect the student to an appropriate UTM office or official webpage. | Run a defined scope test set containing at least 20 out-of-scope queries (e.g. "What is my CGPA?", "Which courses should I take next semester?"). | Refusal-and-redirect success rate | ≥ 95% |

