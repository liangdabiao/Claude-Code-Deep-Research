# Deep Research Implementation - Version 3.0

## Overview

This folder contains Version 3.0 of the Deep Research Implementation with Graph of Thoughts (GoT). It integrates feedback from multiple sources and provides a comprehensive, self-contained system for conducting AI-powered deep research.

## What's New in Version 3.0

### Key Improvements Over V2

| Feature | Description |
|---------|-------------|
| **Phase 0: Question Classification** | Fast-path routing (Type A/B/C/D) to avoid over-engineering simple queries |
| **Research Intensity Tiers** | Quick/Standard/Deep/Exhaustive with automatic resource allocation |
| **Hypothesis Formation** | Phase 1.5 transforms research from information gathering to hypothesis testing |
| **Evidence Ledger** | Claim-to-evidence mapping with C1/C2/C3 taxonomy |
| **Contradiction Triage** | Framework for resolving conflicting sources (4 conflict types) |
| **Independence Grouping** | Prevents "citation laundering" (10 sources citing 1 report) |
| **Implications Engine** | SO WHAT/NOW WHAT/WHAT IF/COMPARED TO analysis |
| **Red Team Agent** | Devil's advocate transformation at depth 3+ |
| **Claim Confidence Scoring** | HIGH/MEDIUM/LOW/SPECULATIVE tags on all claims |
| **Checkpoint Aggregation** | Mid-research coordination at depth 2 |
| **QA Gates** | Pass/fail criteria for each phase |
| **Prompt Injection Defense** | Firewall rules for hostile web content |
| **Domain Overlays** | Specialized protocols for healthcare, financial, legal |
| **Output Format Specification** | JSON export schema, structured data outputs, multiple deliverable formats |
| **Citation Strictness Levels** | Strict/Standard/Light citation modes |
| **Optional Tooling** | Guardrails, caching, vector DB, citation drift detection |

## Folder Structure

```
Version3/
├── README.md                    # This file
├── Claude.md                    # Main implementation (self-contained)
└── DOMAIN_OVERLAYS/
    ├── healthcare.md            # Medical research requirements
    ├── financial.md             # Financial analysis requirements
    ├── legal.md                 # Legal research requirements
    └── market.md                # Market research requirements
```

## How to Use

### Quick Start

1. Copy `Claude.md` to your project as `CLAUDE.md`
2. Say **"Deep research [your topic]"**
3. The system automatically executes all phases

### With Domain Overlays

For specialized research, reference the appropriate overlay:
- Healthcare/Medical: Load `DOMAIN_OVERLAYS/healthcare.md`
- Financial/Investment: Load `DOMAIN_OVERLAYS/financial.md`
- Legal/Regulatory: Load `DOMAIN_OVERLAYS/legal.md`
- Market/Competitive: Load `DOMAIN_OVERLAYS/market.md`

## Automatic Execution Flow

When you say "Deep research [topic]", the system automatically:

1. **Phase 0**: Classifies question complexity (Type A/B/C/D)
2. **Phase 1**: Captures research contract (scope, audience, constraints)
3. **Phase 1.1**: Classifies intensity tier (Quick/Standard/Deep/Exhaustive)
4. **Phase 1.5**: Generates testable hypotheses
5. **Phase 2**: Creates retrieval plan + initializes GoT graph
6. **Phase 3**: Executes iterative querying with GoT operations
7. **Phase 4**: Triangulates sources + resolves contradictions
8. **Phase 5**: Synthesizes with implications + Red Team challenge
9. **Phase 6**: QA with claim verification + numeric audit
10. **Phase 7**: Packages and delivers to `/RESEARCH/[project_name]/`

## Output Structure

All research outputs are delivered in:

```
/RESEARCH/[project_name]/
├── README.md
├── 00_research_contract.md
├── 01_research_plan.md
├── 02_query_log.csv
├── 03_source_catalog.csv
├── 04_evidence_ledger.csv          # Most important artifact
├── 05_contradictions_log.md
├── 06_key_metrics.csv
├── 07_working_notes/
├── 08_report/
│   ├── 00_executive_summary.md
│   ├── 01_context_scope.md
│   ├── 02_findings_current_state.md
│   └── ...
├── 09_qa/
│   ├── qa_report.md
│   ├── citation_audit.md
│   └── numeric_audit.md
└── 10_graph/
    ├── graph_state.json
    └── graph_trace.md
```

## Sources

This version integrates feedback from:
- `claude_advice.md` (priority source for conflicts)
- `chatgpt.md`
- `gemini.md`

## Version History

- **V1.0**: Initial implementation
- **V2.0**: Added GoT implementation, agent templates
- **V3.0**: Integrated all feedback, added hypothesis formation, intensity tiers, evidence ledger, Red Team, domain overlays

---

*Version 3.0 - December 2025*
