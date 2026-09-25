# NexaPay Platform

<div align="center">

[![Java Version](https://img.shields.io/badge/Java-17%20%7C%2021-blue.svg?style=flat-square&logo=openjdk)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.3-6DB33F.svg?style=flat-square&logo=springboot)](https://spring.io/projects/spring-boot)
[![Spring AI](https://img.shields.io/badge/Spring%20AI-1.0.0--M1-6DB33F.svg?style=flat-square&logo=spring)](https://spring.io/projects/spring-ai)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16%20%2B%20pgvector-336791.svg?style=flat-square&logo=postgresql)](https://github.com/pgvector/pgvector)
[![React](https://img.shields.io/badge/Frontend-React%2018%20%2B%20Vite-61DAFB.svg?style=flat-square&logo=react)](https://react.dev/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg?style=flat-square)](LICENSE)

**AI-Powered Card & Payment Operations Intelligence Platform**

*A resilient, modular monolith unifying deterministic financial transaction processing with enterprise AI-driven root-cause investigation, real-time ISO-8583 decline diagnostics, immutable double-entry ledgering, and hybrid vector RAG.*

[Architectural Manifesto](#-architectural-manifesto) • [System Architecture](#-system-architecture) • [Domain Modules](#-core-domain-modules) • [AI Operations Plane](#-ai-operations--intelligence-plane) • [Frontend Dashboard](#-frontend-experience) • [Quickstart](#-getting-started) • [API Reference](#-api-specification)

</div>

---

## 📖 Executive Summary

Modern payment operations and card issuing platforms process millions of transactions per day. When payments fail, chargebacks surge, or settlement mismatches occur, operations analysts are inundated with siloed data across gateway logs, ledger journals, ISO-8583 response codes, KYC profiles, and dispute queues.

**NexaPay** solves this by establishing a high-assurance financial operations platform that bridges **deterministic financial execution** with **autonomous AI root-cause investigation**. It handles end-to-end card lifecycles, ISO-8583 authorizations, double-entry financial accounting, fraud velocity detection, and batch clearing, while equipping Tier-1/Tier-2 support and operations teams with an intelligent AI copilot powered by **Spring AI**, **pgvector RAG**, and **type-safe domain tool calling**.

---

## 🏛 Architectural Manifesto

NexaPay operates on a foundational enterprise architectural principle:

> 🛡️ **"AI investigates, correlates, and explains. Deterministic systems decide and execute."**

The platform strictly decouples all operations into two segregated architectural planes:

1. **The Deterministic Financial Core (ACID Plane):**
   * High-throughput, state-machine-driven transactional engine.
   * Enforces account limits, card validation, ISO-8583 authorization rules, and immutable double-entry ledger bookkeeping.
   * State mutations are strictly deterministic, idempotent, and protected by pessimistic/optimistic concurrency controls.

2. **The AI Operations Intelligence Plane (Read-Only Analytical Plane):**
   * Autonomous investigation orchestrator powered by Spring AI.
   * Restricted to **read-only** execution via strongly-typed, Jakarta-validated `@Tool` methods.
   * Augments investigations with runbook retrieval via PostgreSQL `pgvector` HNSW indexes.
   * Governed by PCI-DSS sensitive data masking (AOP PAN/PII sanitization) and strict Role-Based Access Control (RBAC).

---

## 📐 System Architecture

NexaPay is designed as an enterprise **Modular Monolith** in Java 17/21 + Spring Boot 3.3+, utilizing a unified PostgreSQL 16+ instance that hosts both relational B-Tree tables and vector embeddings (`pgvector`).

```
                                  ┌────────────────────────────────────────────────────────┐
                                  │                  CLIENT / ANALYST                      │
                                  │             (React 18 / Vite Dashboard)                │
                                  └───────────────────────────┬────────────────────────────┘
                                                              │ HTTPS (JWT)
                                                              ▼
┌──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                         NEXAPAY MODULAR MONOLITH (Spring Boot 3.3+)                                     │
│                                                                                                                          │
│  ┌────────────────────────────────────────────────────────┐  ┌────────────────────────────────────────────────────────┐  │
│  │               DETERMINISTIC FINANCIAL CORE             │  │               AI OPERATIONS INTELLIGENCE               │  │
│  │                                                        │  │                                                        │  │
│  │  ┌───────────────┐ ┌───────────────┐ ┌──────────────┐  │  │  ┌───────────────┐ ┌───────────────┐ ┌──────────────┐  │  │
│  │  │ Card Engine   │ │ Payment FSM   │ │ Double-Entry │  │  │  │ Spring AI     │ │ Hybrid RAG    │ │ Type-Safe    │  │  │
│  │  │ & Limits      │ │ & Settlements │ │ Ledger Engine│  │  │  │ Orchestrator  │ │ (pgvector)    │ │ Tools (@Tool)│  │  │
│  │  └───────┬───────┘ └───────┬───────┘ └──────┬───────┘  │  │  └───────┬───────┘ └───────┬───────┘ └──────┬───────┘  │  │
│  │          │                 │                │          │  │          │                 │                │          │  │
│  │  ┌───────▼─────────────────▼────────────────▼───────┐  │  │  ┌───────▼─────────────────▼────────────────▼───────┐  │  │
│  │  │    Deterministic Rules & Fraud Engine            │  │  │  │   AOP PII/PAN Masking & Role Authorization       │  │  │
│  │  └─────────────────────────┬────────────────────────┘  │  │  └─────────────────────────┬────────────────────────┘  │  │
│  └────────────────────────────┼───────────────────────────┘  └────────────────────────────┼───────────────────────────┘  │
│                               │                                                           │                              │
│                               ▼ Read/Write ACID Transactions                              ▼ Read-Only Domain Invocations │
│  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                     DOMAIN SERVICES & REPOSITORIES (Spring Data JPA)                                │  │
│  └────────────────────────────────────────────────────────────┬────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────┘
                                                                │
                                                                ▼
┌──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       UNIFIED POSTGRESQL 16+ STORAGE ENGINE                                              │
│                                                                                                                          │
│  ┌─────────────────────────────────────────────────────────┐  ┌───────────────────────────────────────────────────────┐  │
│  │             RELATIONAL ENGINE (B-TREE INDEXES)          │  │             VECTOR ENGINE (pgvector - HNSW)           │  │
│  │ • Customers, Accounts & Cards (Pessimistic Locks)       │  │ • Standard Operating Procedures (SOP) & Runbooks      │  │
│  │ • Immutable Ledger Entries & Idempotency Store          │  │ • ISO-8583 Decline Code Recovery Manuals              │  │
│  │ • Transactions, Authorizations & Settlements            │  │ • Historical Incident Resolution Embeddings           │  │
│  │ • Append-Only Audit Logs & Security Trails              │  │ • Metric: Cosine Distance (vector_cosine_ops)         │  │
│  └─────────────────────────────────────────────────────────┘  └───────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📦 Core Domain Modules

Every business domain is encapsulated in an isolated module with strictly defined interface contracts:

### 1. Customer & KYC Management (`com.nexapay.customer`)
* Complete customer profile lifecycle management.
* KYC verification tiers (`TIER_1`, `TIER_2`, `TIER_3`) governing maximum card transaction and daily limits.
* Risk profile scoring, Politically Exposed Persons (PEP) status, and sanction checks.

### 2. Card Engine & Limit Management (`com.nexapay.card`)
* Physical and Virtual card provisioning (Visa / Mastercard / RuPay).
* Daily, monthly, and per-transaction spending limit enforcements.
* Multi-channel controls (E-Commerce, POS, ATM, International, Contactless).
* Instant card lifecycle state transitions (`ACTIVE`, `FROZEN`, `BLOCKED`, `EXPIRED`).

### 3. ISO-8583 Authorization Engine (`com.nexapay.authorization`, `com.nexapay.transaction`)
* Real-time ISO-8583 authorization rule evaluation.
* Standardized decline code assignments:
  * `00`: Approved
  * `51`: Insufficient Funds / Credit Limit Exceeded
  * `57`: Transaction Not Permitted to Cardholder (e.g. International channel disabled)
  * `59`: Suspected Fraud (velocity / anomaly trigger)
  * `61`: Exceeds Withdrawal Limit
  * `63`: Security Violation (CVV/PIN mismatch)
  * `91`: System Error / Issuer Unavailable

### 4. Payment Lifecycle State Machine (`com.nexapay.payment`)
* Strict Finite State Machine (FSM) enforcing payment state progression:
  $$\text{INITIATED} \xrightarrow{} \text{AUTHORIZED} \xrightarrow{} \text{CAPTURED} \xrightarrow{} \text{SETTLED}$$
  $$\text{AUTHORIZED} \xrightarrow{} \text{VOIDED} \quad \mid \quad \text{SETTLED} \xrightarrow{} \text{REFUNDED} \quad \mid \quad \text{FAILED}$$
* Idempotency preservation on all mutate operations via `Idempotency-Key` headers.

### 5. Double-Entry Immutable Ledger Engine (`com.nexapay.ledger`)
* Strict financial accounting: every movement of money consists of balanced Debit and Credit entries ($\sum \text{Debits} = \sum \text{Credits}$).
* Account classification:
  * `CUSTOMER_AVAILABLE_BALANCE` / `CUSTOMER_HOLD_BALANCE`
  * `MERCHANT_RECEIVABLE` / `MERCHANT_SETTLEMENT`
  * `INTERCHANGE_EXPENSE` / `NETWORK_ASSESSMENT`
  * `ESCROW_CLEARING`
* Immutable journal entries with cryptographic verification checksums.

### 6. Deterministic Fraud & Velocity Engine (`com.nexapay.fraud`)
* Geovelocity anomaly detection (impossible transit time between merchant physical locations).
* Rapid-fire velocity checks (e.g., $>5$ transactions in under 60 seconds).
* MCC (Merchant Category Code) blacklisting and high-risk merchant identification.

### 7. Settlement & Clearing Reconciliation (`com.nexapay.settlement`)
* Ingestion of batch clearing files from payment networks.
* Automated 3-way reconciliation (Gateway vs. Core Ledger vs. Scheme Clearing File).
* Discrepancy flagging for fee variances, missing settlements, and currency conversion mismatches.

### 8. Chargeback & Dispute Lifecycle (`com.nexapay.chargeback`)
* Dispute filing, evidence submission, and representment tracking.
* Integration with network dispute reason codes (e.g., 10.4 Fraud - Card-Not-Present, 13.1 Services Not Provided).
* Real-time ledger freeze on disputed settlement balances.

### 9. Immutable Audit Trail (`com.nexapay.audit`)
* Tamper-evident, append-only operational audit log.
* Records analyst actions, AI investigations, tool invocations, and manual balance overrides.

---

## 🧠 AI Operations & Intelligence Plane

NexaPay harnesses **Spring AI** to deliver deterministic, auditable, and secure AI investigation capabilities.

```
                  ┌────────────────────────────────────────────────────────┐
                  │                 User Query / Incident Ref              │
                  │   "Why was TXN-84721 declined at Hermes Paris?"        │
                  └───────────────────────────┬────────────────────────────┘
                                              │
                                              ▼
                  ┌────────────────────────────────────────────────────────┐
                  │            Spring AI ChatClient Orchestrator           │
                  └───────────────┬────────────────────────┬───────────────┘
                                  │                        │
       ┌──────────────────────────┴────────┐      ┌────────┴──────────────────────────┐
       │ Vector RAG Context (pgvector)     │      │ Type-Safe Tools (@Tool)           │
       │ • Policy Runbooks                 │      │ • getTransactionDetails()         │
       │ • ISO-8583 Decline SOPs           │      │ • getCardLedgerSummary()          │
       │ • Fraud Threshold Guidelines      │      │ • getFraudRiskAssessment()        │
       └───────────────────────────────────┘      └─────────────────┬─────────────────┘
                                                                    │
                                                  ┌─────────────────▼─────────────────┐
                                                  │ AOP Sensitive Data Masking        │
                                                  │ • PAN: 4111********1111           │
                                                  │ • Customer PII Redacted           │
                                                  └─────────────────┬─────────────────┘
                                                                    │
                                                  ┌─────────────────▼─────────────────┐
                                                  │ Domain Service (Read-Only)        │
                                                  └───────────────────────────────────┘
                                              │
                                              ▼
                  ┌────────────────────────────────────────────────────────┐
                  │         Structured Output Record (JSON / DTO)          │
                  │  • Root Cause: Card international channel disabled     │
                  │  • Evidence: ISO-8583 Code 57 + Card Settings Flag     │
                  │  • Action Plan: Guide customer to enable roaming toggle│
                  │  • Confidence: 0.96 (High)                             │
                  └────────────────────────────────────────────────────────┘
```

### 🛠️ Type-Safe AI Tool Catalog

All tools are annotated with `@Tool`, require authenticated security roles, validate inputs via Jakarta Validation, and pass through PAN-masking AOP aspects:

| Tool Name | Security Role | Purpose |
| :--- | :--- | :--- |
| `getTransactionDetails` | `OPERATIONS_ANALYST` | Retrieves verified transaction metadata, authorization outcome, ISO-8583 code, and merchant details. |
| `getCardLedgerSummary` | `OPERATIONS_ANALYST` | Inspects card status, daily/monthly spend against limits, and hold balances. |
| `getCustomerProfile` | `SUPPORT_AGENT` | Reads customer KYC tier, risk rating, and account status with masked PII. |
| `getCustomerRecentTransactions` | `SUPPORT_AGENT` | Examines recent transaction history and velocity patterns. |
| `getPaymentTimeline` | `OPERATIONS_ANALYST` | Inspects lifecycle state transitions (Initiate $\to$ Authorize $\to$ Capture). |
| `getFraudRiskAssessment` | `FRAUD_ANALYST` | Evaluates deterministic risk scores, geovelocity anomalies, and rule matches. |
| `getSettlementDiscrepancy` | `OPERATIONS_ANALYST` | Identifies batch clearing mismatches and fee variances. |
| `getChargebackDetails` | `OPERATIONS_ANALYST` | Fetches dispute status, evidence deadlines, and contested amounts. |
| `getAuditHistory` | `AUDITOR` | Queries append-only audit records for root-cause provenance. |

### 🔍 Hybrid RAG via pgvector

NexaPay maintains organizational standard operating procedures (SOPs), dispute playbooks, and ISO-8583 decline handling manuals in PostgreSQL using `pgvector`:
* **Index Method:** HNSW (Hierarchical Navigable Small World) for sub-millisecond approximate nearest neighbor searches.
* **Distance Metric:** Cosine Distance (`vector_cosine_ops`).
* **Embeddings Support:** OpenAI `text-embedding-3-small` (1536 dim) / Google Gemini Embeddings (768 dim) / Ollama.

### 🧪 Ground-Truth Benchmark & Evaluation Suite

To guard against hallucination and ensure high fidelity in production, NexaPay includes an automated AI Evaluation benchmark suite (`AiInvestigationBenchmarkTest`):
* Evaluates root-cause accuracy across ground-truth incident datasets (International limits, Geovelocity fraud, Ledger hold mismatches, Insufficient balance).
* Enforces minimum accuracy thresholds ($>90\%$) and zero tolerance for hallucinated PANs or parameters.

---

## 💻 Frontend Experience

The NexaPay frontend is a high-performance React 18 + Vite application designed specifically for payment operations centers:

* **AI Incident Copilot & Investigation Workspace:** Interactive diagnostic terminal providing instant root-cause breakdowns, cited evidence trails, and recommended corrective actions.
* **Double-Entry Ledger Explorer:** Visual representation of journal batches, account balance trees, and credit/debit audit verification.
* **Fraud & Risk Intelligence Dashboard:** Geovelocity anomaly visualization, real-time transaction velocity streams, and dispute triage.
* **AI Evaluation & Telemetry Suite:** Real-time metrics monitoring LLM response latency, tool invocation success rate, and benchmark accuracy scores.

---

## 🛠 Technology Stack

### Backend
* **Language & Runtime:** Java 17 / 21 LTS
* **Framework:** Spring Boot 3.3.3
* **AI Framework:** Spring AI 1.0.0-M1 (ChatClient, Type-Safe Tools, pgvector store)
* **Persistence:** Spring Data JPA / Hibernate 6 (Batching, Optimistic/Pessimistic Locking)
* **Database & Vector Store:** PostgreSQL 16+ with `pgvector` extension
* **Migrations:** Flyway Database Migrations
* **Resilience & Fault Tolerance:** Resilience4j (Circuit Breakers, Bulkheads, Rate Limiters, Retries)
* **Security & Auth:** Spring Security 6, JJWT (JSON Web Tokens), Bucket4j (Token Bucket Rate Limiting)
* **Observability:** Spring Boot Actuator, Micrometer, Prometheus Metrics, Logback

### Frontend
* **Core:** React 18 + TypeScript + Vite
* **State & Data Fetching:** TanStack React Query v5
* **Data Visualization:** Chart.js + react-chartjs-2
* **Icons & Aesthetics:** Lucide React, Modern Dark/Glassmorphism CSS Design System

### Testing & QA
* **Architecture Validation:** ArchUnit 1.3.0 (enforcing modular package boundaries)
* **Testing:** JUnit 5, Mockito, Spring Security Test, Spring Boot Starter Test

---

## 🚀 Getting Started

### Prerequisites
* **Java:** OpenJDK 17 or 21 installed (`java -version`)
* **Node.js:** Node.js 18+ and npm (`node -v`)
* **Docker:** Docker Desktop & Docker Compose (`docker-compose -v`)
* **AI API Key:** Google Gemini API Key (`GEMINI_API_KEY`) or OpenAI API Key (`OPENAI_API_KEY`)

---

### Step 1: Clone & Configure Environment

```bash
git clone https://github.com/your-org/nexapay.git
cd nexapay
```

Set your AI provider API key:
```bash
# For Windows PowerShell
$env:GEMINI_API_KEY="your-gemini-api-key"

# For Linux/macOS
export GEMINI_API_KEY="your-gemini-api-key"
```

---

### Step 2: Launch PostgreSQL with `pgvector` via Docker

NexaPay includes a pre-configured `docker-compose.yml` that boots PostgreSQL 16 with the `pgvector` extension:

```bash
docker-compose up -d nexapay-postgres
```

Verify the database container is healthy:
```bash
docker ps --filter name=nexapay_postgres
```

---

### Step 3: Run the Backend Platform

Start the Spring Boot backend using the Maven wrapper:

```bash
# Windows
.\mvnw.cmd spring-boot:run

# Linux / macOS
./mvnw spring-boot:run
```

The backend will start at `http://localhost:8080`.
* **Swagger / OpenAPI Documentation:** `http://localhost:8080/swagger-ui.html`
* **Health & Metrics Endpoint:** `http://localhost:8080/actuator/health`

---

### Step 4: Run the Frontend Dashboard

In a new terminal window:

```bash
cd frontend
npm install
npm run dev
```

The dashboard will be available at `http://localhost:5173`.

---

### Alternative: Run Everything in Docker

You can launch both the PostgreSQL database and the fully packaged backend application together:

```bash
docker-compose up --build
```

---

## 🔒 Security, Compliance & Governance

1. **PCI-DSS Compliance (AOP PAN Masking):**
   * Primary Account Numbers (16-digit PANs) are automatically masked across all system boundaries: `4111222233331111` $\to$ `4111********1111`.
   * Card CVV / PIN data is **never** logged or injected into LLM context prompts.

2. **Role-Based Access Control (RBAC):**
   * `ADMIN`: Full system administration, benchmark execution, and RAG re-indexing.
   * `OPERATIONS_ANALYST`: Transaction diagnostics, card limits, payment lifecycle triage, and ledger analysis.
   * `FRAUD_ANALYST`: Fraud signal assessment, velocity investigation, and dispute review.
   * `SUPPORT_AGENT`: Customer profile reading and recent transaction summaries.
   * `AUDITOR`: Immutable audit log examination.

3. **Resilience & Rate Limiting:**
   * Resilience4j circuit breakers prevent cascade failures during upstream AI or payment gateway degradation.
   * Bucket4j token-bucket algorithms prevent endpoint abuse.

4. **Standardized RFC 9457 Problem Details:**
   * All API errors return structured `application/problem+json` payloads containing machine-readable `errorCode`, HTTP status, and detailed context.

---

## 📡 API Specification

### Authentication & Authorization
* `POST /api/v1/auth/login` — Authenticate and obtain JWT token.
* `GET  /api/v1/auth/me` — Retrieve authenticated user profile and roles.

### Card & Account Management
* `GET  /api/v1/cards/{id}` — Fetch card details and spend limit status.
* `POST /api/v1/cards` — Provision a new physical or virtual card.
* `POST /api/v1/cards/{id}/block` — Block/Freeze card (requires `Idempotency-Key`).
* `PUT  /api/v1/cards/{id}/limits` — Update channel toggles and transaction limits.

### Transactions & Authorizations
* `GET  /api/v1/transactions` — Query transaction log with filtering and pagination.
* `POST /api/v1/transactions/authorize` — Process real-time ISO-8583 card authorization.
* `GET  /api/v1/transactions/{id}/authorization` — Retrieve ISO-8583 decline details.

### Double-Entry Ledger
* `GET  /api/v1/ledger/accounts/{id}/entries` — View immutable double-entry journal entries.
* `GET  /api/v1/ledger/accounts/{id}/balance` — Calculate verified real-time balance.

### AI Operations Copilot
* `POST /api/v1/ai/investigate` — Trigger autonomous AI investigation for a transaction or incident.
* `POST /api/v1/ai/chat` — Contextual AI chat assistant with multi-turn tool invocation.
* `POST /api/v1/ai/rag/reindex` — Re-index runbooks and SOP documents into `pgvector`.

### Admin & Benchmarking
* `POST /api/v1/admin/seed` — Seed demo transactions, incidents, cards, and runbooks.
* `POST /api/v1/admin/benchmark/run` — Run automated AI evaluation ground-truth benchmarks.

---

## 🧪 Testing & Architectural Verification

NexaPay enforces strict automated quality gates:

### 1. ArchUnit Boundary Tests
Ensures package boundaries in the modular monolith remain uncompromised:
```bash
./mvnw test -Dtest=ArchitectureBoundaryTest
```

### 2. AI Investigation Ground-Truth Benchmark
Validates LLM precision, tool selection, and absence of hallucination:
```bash
./mvnw test -Dtest=AiInvestigationBenchmarkTest
```

### 3. Run All Tests
```bash
./mvnw test
```

---

## 📂 Project Structure

```text
nexapay/
├── docs/                                # Technical Architecture & Specification Specs
│   ├── 02-ARCHITECTURE.md               # System Architecture Specification
│   ├── 03-DOMAIN-MODEL.md               # Domain Models & Entities
│   ├── 04-DATABASE-DESIGN.md            # PostgreSQL & pgvector Schema Design
│   ├── 05-AI-DESIGN.md                  # Spring AI Architecture & RAG Protocol
│   ├── 06-AI-TOOLS.md                   # AI Tool Catalog & Tool Contracts
│   ├── 07-DATA-GENERATION.md            # Demo Data Generator Specs
│   └── DPR.md                           # Detailed Project Report & Requirements
│
├── frontend/                            # React 18 + Vite Operations Dashboard
│   ├── src/
│   │   ├── api/                         # Backend API Clients & Query Hooks
│   │   ├── components/
│   │   │   ├── copilot/                 # AI Incident Investigation Terminal
│   │   │   ├── ledger/                  # Double-Entry Ledger Explorer
│   │   │   ├── fraud/                   # Fraud & Geovelocity Map / Alerts
│   │   │   └── benchmark/               # AI Benchmark & Accuracy Analytics
│   │   ├── types/                       # TypeScript Domain & AI Interfaces
│   │   └── App.tsx                      # Main Application Router & Layout
│   ├── package.json
│   └── vite.config.ts
│
├── src/main/java/com/nexapay/           # Java 17/21 Spring Boot Application
│   ├── ai/                              # AI Orchestration, Tools & pgvector RAG
│   ├── audit/                           # Append-Only Immutable Audit Log
│   ├── auth/                            # JWT Security & RBAC Configuration
│   ├── authorization/                   # ISO-8583 Authorization Decision Engine
│   ├── card/                            # Card Provisioning & Limit Enforcement
│   ├── chargeback/                      # Dispute & Evidence Lifecycle
│   ├── common/                          # AOP Masking, Exceptions, Idempotency
│   ├── customer/                        # Customer KYC & Risk Profiles
│   ├── fraud/                           # Geovelocity & Deterministic Fraud Engine
│   ├── generator/                       # Synthetic Scenario & Data Generator
│   ├── ledger/                          # Double-Entry Accounting Journal Engine
│   ├── merchant/                        # Merchant Accounts & Terminals
│   ├── payment/                         # Payment Finite State Machine (FSM)
│   ├── settlement/                      # Batch Clearing & 3-Way Reconciliation
│   ├── transaction/                     # Transaction Ingestion & Event Storage
│   └── NexaPayApplication.java          # Spring Boot Main Entrypoint
│
├── src/main/resources/
│   ├── application.yml                  # Central Spring Boot & Spring AI Configuration
│   ├── db/migration/                    # Flyway Database Migration Scripts
│   └── docs/                            # Markdown SOP Runbooks for Vector Store RAG
│
├── Dockerfile                           # Production Multi-Stage Container Build
├── docker-compose.yml                   # PostgreSQL (pgvector) + Application Compose
└── pom.xml                              # Maven Project Dependencies & Plugins
```

---

## 📄 License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
