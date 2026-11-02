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
| 1 | Academic FAQ Chatbot Using Retrieval-Augmented Generation (2026) | Uses document preprocessing, semantic retrieval, a vector database, and an LLM to answer academic questions. |
| 2 | Retrieval-Augmented Generation for Educational Applications — A Systematic Survey (2025) | Reviews RAG applications in education, including document indexing, retrieval, and answer generation. |
| 3 | Label-Free Evaluation of Retrieval-Augmented Generation in a University Policy Chatbot (2026) | Evaluates university policy chatbot performance through answer relevance, groundedness, and retrieval effectiveness. |

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




