# UniGuide AI — AI-Powered University FAQ Assistant

> Answers UTM students' questions about academic calendars, course registration, academic regulations, and student services using official university documents.

## Problem Statement

UTM students may need to consult multiple university webpages and documents to find information about academic calendars, course registration, academic regulations, and student services. Although official information is available, it is distributed across different sources, making it less convenient to locate relevant answers. UniGuide AI uses retrieval-augmented generation (RAG) to provide answers grounded in official UTM documents, with source citations and a fallback when sufficient evidence is unavailable.

## Evidence of the Problem

- UTM's Academic Management Division publishes course registration guidelines and procedures on its official website.  
  Source: https://amd.utm.my/course-registration/
- UTM's Faculty of Computing Student Development Hub provides links to student resources, including academic calendars, academic regulations, and course registration information.  
  Source: https://comp.utm.my/student/home/

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

- **O1:** To collect and prepare official UTM documents related to academic calendars, course registration, academic regulations, and student services for the knowledge base.
- **O2:** To develop a web-based AI FAQ assistant using retrieval-augmented generation that provides source-grounded answers with citations and an insufficient-evidence fallback.
- **O3:** To evaluate the system based on answer correctness, retrieval relevance, citation accuracy, unsupported-answer handling, and response latency.

## Target Users

UTM undergraduate students, especially new students who need to find information about academic calendars, course registration, academic regulations, and student services.

## Data Sources

- **UTM Academic Management Division — Course Registration Guidelines:** https://amd.utm.my/course-registration/
- **UTM Faculty of Computing — Student Development Hub:** https://comp.utm.my/student/home/
- **UTM Faculty of Computing — Undergraduate Resources:** https://comp.utm.my/undergraduate/
- **UTM Faculty of Computing — Programmes and Student Hub:** https://comp.utm.my/programmes-and-student-hub/

## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | System map + project scope | README.md + docs | ✅ |
| W2 | Evaluation criteria + requirements + architecture decisions + architecture v1 | docs | ✅ |
| W3 | Data inventory + pipeline decisions + pipeline diagram | docs + data + notebook | ✅ |
