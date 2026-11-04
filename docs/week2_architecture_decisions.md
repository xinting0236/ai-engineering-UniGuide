# Architecture Decision Record (ADR)

### Decision 1: Include an Official Knowledge Source and Semantic Retrieval

| Item | Description |
|---|---|
| Decision | Include a knowledge source and semantic document retrieval component. |
| Requirement / Constraint | FR1, FR2, NFR1, NFR3 — Answers must use relevant and official UTM information. |
| Alternatives Considered | General LLM without external documents; keyword-based search; semantic document retrieval. |
| Selected Option | Semantic document retrieval connected to an official UTM knowledge base. |
| Trade-off Accepted | Increases system complexity and requires document preparation and indexing, but improves access to relevant university information and reduces dependence on the LLM's general knowledge. |

### Decision 2: Include Evidence Checking and Fallback Control

| Item | Description |
|---|---|
| Decision | Include evidence checking and fallback control before generating or delivering answers. |
| Requirement / Constraint | FR4, RAI1 — The system must avoid guessing when sufficient supporting information is unavailable. |
| Alternatives Considered | Allow the LLM to answer every question; use prompt instructions only; include explicit evidence checking and fallback control. |
| Selected Option | Explicit evidence checking with fallback behaviour. |
| Trade-off Accepted | Adds processing steps and may occasionally reject questions that could have been answered, but reduces unsupported answers and hallucinations. |

### Decision 3: Include Citation and Source Control

| Item | Description |
|---|---|
| Decision | Include citation and source control for document-grounded answers. |
| Requirement / Constraint | FR3, RAI1, RAI2 — Answers must provide verifiable source references and available document metadata. |
| Alternatives Considered | Answers without citations; general document links; specific source references with available document dates or versions. |
| Selected Option | Attach specific source references and available document metadata to generated answers. |
| Trade-off Accepted | Requires source tracking and additional output processing, but allows students to verify information using official UTM documents. |

### Decision 4: Include Scope Control

| Item | Description |
|---|---|
| Decision | Include an explicit scope control component to identify questions outside the supported domain. |
| Requirement / Constraint | FR6, RAI4 — Out-of-scope questions (private records, personalised advising, transactions) must be declined and redirected. |
| Alternatives Considered | Allow unrestricted questions; rely only on LLM instructions; include explicit scope control. |
| Selected Option | Explicit scope control with fallback responses for out-of-scope questions. |
| Trade-off Accepted | May wrongly refuse some relevant questions because of ambiguous wording, and adds processing, but reduces inappropriate responses outside the intended scope. |

### Decision 5: Include Document Management and Knowledge Base Update (NEW)</mark>

| Item | Description |
|---|---|
| Decision | Include a document management component that updates the knowledge base and stores source dates and versions. |
| Requirement / Constraint | NFR4, RAI2, O1 — Updated official documents must be reflected within 10 minutes, and dates or versions must be available for display. |
| Alternatives Considered | One-time static document load; manual rebuild of the whole knowledge base; managed per-document update with metadata. |
| Selected Option | Managed per-document update with stored source metadata. |
| Trade-off Accepted | Needs extra metadata handling and an update process, but avoids outdated answers and supports date or version display. |

### Decision 6: Include an Evaluation Point

| Item | Description |
|---|---|
| Decision | Include an evaluation point to assess the system's performance. |
| Requirement / Constraint | O3, NFR1, NFR2, NFR3 — The system must meet defined correctness, retrieval relevance, and latency targets. |
| Alternatives Considered | Evaluate manually without a defined test set; use a prepared evaluation dataset with defined metrics. |
| Selected Option | Use a prepared evaluation dataset and defined evaluation metrics. |
| Trade-off Accepted | Requires time to prepare test questions and verified reference answers, but provides measurable evidence of system performance. |

---

# Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | NFR4 / RAI2 | Knowledge Base Update | Document Management and Knowledge Base Update | Document update test: ≤ 10 min per document; source date/version display 100%. |
| O3 | FR1 / FR2 / NFR3 | Document Retrieval Relevance | Knowledge Source and Semantic Document Retrieval | Recall@5 test results; target ≥ 80%. |
| O2 | FR3 / RAI1 | Source Citation and Support | Language Model Service and Citation and Source Control | Citation presence and citation-source match results; targets 100% and ≥ 95%. |
| O2 | FR4 / RAI1 | Insufficient-Evidence Fallback | Evidence Checking and Fallback Control | Correct fallback test results; target ≥ 95%. |
| O2 | FR6 / RAI4 | Out-of-Scope Refusal | Scope Control and Fallback Control | Refusal-and-redirect results on ≥ 20 out-of-scope queries; target ≥ 95%. |
| O3 | NFR1 | Answer Correctness | Semantic Retrieval, Evidence Checking, and Language Model Service | Answer correctness test results; target ≥ 85%. |
| O3 | NFR2 | Response Latency | Application Interface and AI Processing Components | p90 response latency test results; target ≤ 5 seconds. |

