# Portfolio & Recruiter Agent (Capstone Project)

**Author:** NAWAF ABDULLAH BINTALEB  
**Program:** SDAIA Academy Building AI Systems / Agentic Workflows  
**Cohort Dates:** Spring 2026  
**Academy:** [SDAIA Academy](https://github.com/SDAIAAcademy)

---

## Executive Summary

The **Portfolio & Recruiter Agent** is an agentic, multi-domain Retrieval-Augmented Generation (RAG) system built with **LangGraph Functional API** and **LangChain**. Designed to handle multi-turn interactions with recruiters and technical leads, the agent routes queries dynamically between domain-isolated vector databases, preserves cross-thread long-term memory, and enforces Human-in-the-Loop (HITL) safeguards before executing high-impact actions like scheduling calendar invites.

---

## Architectural Rationale & Design Choices

### Section 1: Setup and Environment Configuration
> **Rationale:** Standardizing package dependencies across `langgraph`, `langchain`, `fastembed`, and vector stores ensures runtime compatibility across various deployment environments. Isolating configuration flags (such as enabling LangSmith tracing via `LANGCHAIN_TRACING_V2`) early in the pipeline provides complete observability across agent executions without scattering environment variables inside core logic cells[cite: 1].

### Section 2: API Key Configuration & Observability
> **Rationale:** Storing sensitive credentials in Colab user data or environment variables prevents credential leakage in public version control systems[cite: 1]. Incorporating upfront assertions on required keys (`OPEN_ROUTER_API_KEY`, `LANGCHAIN_API_KEY`) implements a fail-fast pattern, stopping downstream execution immediately if required external services are missing rather than throwing cryptic failures during sub-agent tasks[cite: 1].

### Section 3: OpenRouter LLM Initialization & Sanity Testing
> **Rationale:** Utilizing `ChatOpenAI` backed by OpenRouter provides flexibility to swap underlying model providers without refactoring application logic[cite: 1]. Executing an explicit connection test before graph compilation validates API quota, header configurations, and system latency upfront, establishing a reliable baseline for complex structured output generation downstream[cite: 1].

### Section 4: Domain-Isolated RAG Implementation for CV Agent
> **Rationale:** Vector database isolation is critical to avoid context bleeding between structured professional history and informal technical writings[cite: 1]. Utilizing `FastEmbedEmbeddings` (`bge-small-en-v1.5`) alongside `Chroma` provides lightweight, fast semantic retrieval without heavy system dependencies, while a 300-character chunk size with a 50-character overlap maintains concise, high-precision context for resume queries[cite: 1].

### Section 5: Portfolio Synthetic Data Engineering
> **Rationale:** Structuring separate source markdown files for blogs, project architecture case studies, and engineering principles mimics a real-world production database or CMS pipeline[cite: 1]. This separation ensures that the portfolio sub-agent receives isolated domain context specifically curated for technical questions without diluting retrieval quality with resume metadata[cite: 1].

### Section 6: Portfolio Vector Store RAG Setup
> **Rationale:** Reusing the optimized `FastEmbed` embedding model across vector stores guarantees consistent vector space dimensions while maintaining strict collection boundary segregation (`portfolio_store` vs. `cv_store`)[cite: 1]. Storing markdown documents through structured `DirectoryLoader` and `TextLoader` pipelines allows technical content to retain formatting headers, which improves chunk relevance during similarity search[cite: 1].

### Section 7: Retrieval Verification & Similarity Testing
> **Rationale:** Automated unit testing of vector stores via similarity assertions validates that retrieval nodes return non-empty, contextually relevant chunks prior to graph integration[cite: 1]. Testing retrieval boundaries independently isolates database issues from LLM orchestration logic, significantly easing debugging during system construction[cite: 1].

### Section 8: Router & Worker Task Definitions
> **Rationale:** Enforcing structured output parsing via Pydantic (`RouteDecision`) replaces unreliable string matching with deterministic schema-validated routing decisions[cite: 1]. Defining worker tasks with exponential backoff retry policies (`RetryPolicy`) ensures resilience against transient API rate limits or network blips when querying sub-agent tools[cite: 1].

### Section 9: LangGraph Supervisor Graph with HITL & Memory
> **Rationale:** Adopting LangGraph's Functional API (`@entrypoint` and `@task`) provides declarative graph orchestration with native state persistence[cite: 1]. Decoupling short-term conversational threads (`MemorySaver`) from long-term profile stores (`InMemoryStore`) ensures candidate and recruiter facts persist indefinitely across separate interaction threads, while `interrupt()` creates an essential safety checkpoint requiring explicit authorization before performing non-reversible operations like scheduling interviews[cite: 1].

### Section 10: Cross-Thread Long-Term Memory Verification
> **Rationale:** Testing long-term memory access across disjoint thread IDs validates that user metadata (such as recruiter name and company) persists in global store namespaces independently of ephemeral conversation threads[cite: 1]. This pattern allows the agent to maintain personal context across multi-session recruiter interactions without bloating thread checkpoint histories[cite: 1].

### Section 11: Human-In-The-Loop Interrupt & Resume Workflow
> **Rationale:** Simulating interview booking pauses via `interrupt()` and resuming execution via `Command(resume=...)` proves that stateful agent execution can be safely suspended for human authorization[cite: 1]. This architecture prevents autonomous agents from taking unintended external actions while ensuring seamless execution continuity upon approval[cite: 1].

### Section 12: Master End-to-End Test Suite Execution
> **Rationale:** Running an end-to-end integration test suite across all sub-agent routes, memory layers, and interrupt cycles guarantees system-wide regression resistance[cite: 1]. Programmatic assertions across all 5 key interaction scenarios ensure that the compiled supervisor graph fulfills all architectural requirements reliably under production conditions[cite: 1].

---

## Repository Structure

```text
.
├── NAWAF_ABDULLAH_BINTALEB_SDAIA_ACADEMY_BUILDING_AI_SYSTEMS.ipynb   # Master Google Colab notebook[cite: 1]
├── CV_FILES/                                                         # Source directory for candidate CV PDFs[cite: 1]
├── portfolio_files/                                                  # Source directory for project & blog markdown files[cite: 1]
├── chroma_db/                                                        # Persisted Chroma vector database storage[cite: 1]
└── README.md                                                         # Documentation & architectural rationale[cite: 1]
