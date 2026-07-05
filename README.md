# AI Product Decision & Delivery Copilot

A low-code AI workflow that transforms unstructured customer feedback, partner proposals, and solution requirements into structured, prioritized, backlog-ready product recommendations — with transparent scoring, independent AI quality review, and human approval built in from day one.

**Built with:** [n8n](https://n8n.io) (workflow orchestration) + [Claude](https://www.anthropic.com/claude) (Anthropic) + Google Sheets (data store)

---

## The Problem

Product and solutions teams receive Voice-of-Customer input from disconnected sources — support tickets, sales feedback, partner proposals, competitive research, and meeting notes — with no consistent way to qualify it against evidence, technical context, and business justification. Consolidating the inputs isn't the hard part; **qualifying them consistently is.** The result: growing backlogs, priority shifts without defensible justification, and delayed partner/customer commitments.

## Who It's For

- **Primary:** Technical Product Managers / Product Owners
- **Secondary:** Solutions Architects (customer/partner-facing), Ecosystem/GTM Product Managers

## What It Does

1. Accepts feedback through a web form
2. Extracts structured analysis: problem, persona, use case, business impact, product theme, dependencies, risks, missing information, evidence, and confidence — with unknowns explicitly labeled **"Needs Validation"** rather than invented
3. Scores the opportunity across 6 criteria (1–5 scale), each with a **written rationale**
4. Drafts sprint-ready backlog artifacts: epic, user stories, acceptance criteria, non-functional requirements, open questions, and Definition of Done
5. Runs an **independent second AI pass** that critiques the first output for unsupported claims, unjustified scores, untestable acceptance criteria, and missing dependencies
6. Persists everything — inputs, AI output, and human decisions — to a structured, traceable data store
7. Requires a **human decision** (approve / edit / reject / reanalyze) before anything is considered final

## Architecture

```
Web Form (n8n)
     │
     ▼
Structured AI Analysis (Claude)
     │   → Problem, Persona, Use Case, Business Impact,
     │     Product Theme, Dependencies, Risks, Missing Info,
     │     Evidence, Confidence
     ▼
Transparent Prioritization Scoring (Claude)
     │   → 6 criteria, 1–5 scale, each with written rationale
     ▼
Backlog Artifact Generation (Claude)
     │   → Epic, User Stories, Acceptance Criteria, NFRs,
     │     Open Questions, Definition of Done
     ▼
Independent Quality Review (Claude — 2nd pass)
     │   → Checks for unsupported claims, score justification,
     │     untestable acceptance criteria, missing dependencies
     ▼
Persistent Storage (Google Sheets)
     │   → Raw Inputs | AI Recommendations | Human Decisions | Test Results
     ▼
Human Approval (manual review)
         → Approve / Edit / Reject / Reanalyze
```

## AI Role vs. Human Role

| | Responsibility |
|---|---|
| **AI** | Extracts structured information, scores opportunities transparently, drafts backlog-ready artifacts, and critiques its own output via an independent second pass |
| **Human** | Makes every final decision — approve, edit, reject, or request reanalysis. The AI is never permitted to self-approve. |

## Safety & Grounding Controls

- Unknown information is explicitly labeled `Needs Validation` rather than invented
- Every prioritization score requires a specific, evidence-based rationale — no unexplained numbers
- An independent Quality Reviewer checks the primary output before it ever reaches a human

## Testing

Four test cases were run manually, covering happy-path, missing-data, and adversarial scenarios:

| Test | Scenario | Result |
|---|---|---|
| T1 | Rich, detailed feedback | ✅ Pass — full structured output correctly generated |
| T2 | Vague, low-detail feedback | ✅ Pass — fields correctly marked "Needs Validation," no invented specifics |
| T5 | Quality review of T1's output | ✅ Pass — reviewer caught 4 unsupported claims, 3 score-justification issues, 3 untestable acceptance criteria |
| T3 | Minimal one-line input | ❌ **Fail** — backlog artifacts made confident claims not grounded in the input, despite the analysis step correctly scoring low confidence. Caught by the Quality Reviewer. Root cause and a specific prompt correction documented. |

> Testing was conducted manually by submitting cases and evaluating output against expected behavior — this reflects real-world manual QA practice, not automated regression testing.

## Validated Across Three Scenarios

The same core prompt architecture, unmodified, was tested successfully across:

- **SaaS Product Feedback** — SSO/identity access management requirement
- **AI/ISV Partner Evaluation** — data pipeline vendor with unverified compliance claims
- **Enterprise Solution Assessment** — regional bank hybrid cloud migration under regulatory constraints

## Known Limitations

- Human approval is currently a manual spreadsheet entry, not a dedicated interactive UI
- AI output is stored as a single text block rather than parsed into individually searchable fields
- Single-record processing only — no batch/bulk analysis
- The workflow currently requires manual arming for each test submission; not yet published as an always-on production tool
- One documented test failure (T3) reflects a real, unresolved gap between the analysis and backlog-generation steps

## Roadmap

- [ ] Interactive approval UI (Claude Artifact or lightweight web app)
- [ ] Multi-source ingestion: CSV upload, email forwarding, Jira/Salesforce connectors
- [ ] Structured JSON output from Claude for proper field-level storage
- [ ] LangChain-based orchestration for more complex, multi-step agentic workflows
- [ ] Automated theme clustering across multiple feedback records
- [ ] Publish to a production URL for always-on availability

## Data & Privacy

All sample data used in development and testing is **synthetic**. No confidential employer, customer, or partner information was used at any stage of this project.

---

*Built as a portfolio project to demonstrate AI-assisted product discovery, transparent prioritization, and responsible human-in-the-loop design for Technical Product Manager, Product Owner, and Solutions Architect roles.*
