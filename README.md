# Legal-Workflow
# Legal Workflow & WhatsApp AI Agent

An automated, state-driven legal intake assistant built with **LangGraph** and **FastAPI**. The system integrates directly with the WhatsApp Business Cloud API to parse incoming client inquiries, evaluate case urgency, and deliver legally grounded responses using a **Hybrid RAG** pipeline powered by **PostgreSQL (`pgvector` + `tsvector`)**.

---

## Key Features

* **Asynchronous Webhook Processing:** FastAPI endpoint designed to immediately handle Meta's verification challenge and ingest inbound messages without timeout drops.
* **Deterministic LangGraph Workflow:** Replaces fragile chains with a state machine governing the exact sequence: Intake Parsing $\rightarrow$ Urgency Triage $\rightarrow$ Statutory Retrieval $\rightarrow$ Response Generation.
* **Intelligent Urgency Routing:** Employs Pydantic structured extraction to classify case types and instantly escalate high-urgency matters (e.g., imminent court hearings or statute of limitations deadlines).
* **Hybrid Statutory RAG:** Combines dense semantic vector search (`pgvector`) with sparse full-text keyword indexing (`tsvector`) using Reciprocal Rank Fusion (RRF) to retrieve exact legal articles and statutory codes.
* **Grounded Legal Scripting:** Constrains LLM outputs strictly to retrieved legal provisions, automatically appending requisite administrative disclaimers.

---

## Architecture Flow

```mermaid
flowchart TD
    A[WhatsApp User] -->|Inbound Message| B(FastAPI Webhook)
    B -->|Async Dispatch| C[LangGraph Engine]
    
    subgraph State Graph Execution
        C --> D[1. Intake & Parse Node]
        D --> E{Urgency Evaluation}
        E -->|Critical / Immediate| F[Escalate to Human Counsel]
        E -->|Standard Inquiry| G[2. Hybrid RAG Node]
        G <--> H[(PostgreSQL: pgvector + tsvector)]
        G --> I[3. Response Synthesis Node]
        F --> I
    end
    
    I --> J[Meta WhatsApp Cloud API]
    J -->|Outbound Script| A
