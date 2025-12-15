# Deep Research Process V2
## Update Implementation Guide — Applying V1 Feedback

| | |
|---|---|
| **Prepared For** | Technical Implementation Team |
| **Date** | December 14, 2025 |
| **Document Type** | Implementation Specification with Code Additions |

---

# Executive Summary

Version 2 of the Deep Research process made significant improvements over V1, particularly in Graph of Thoughts (GoT) implementation and agent orchestration. However, nine critical gaps identified in the V1 review remain unaddressed. This document provides the exact additions needed to close those gaps and create a world-class research process.

## What V2 Improved Over V1

| Improvement Area | What V2 Added |
|------------------|---------------|
| **GoT Implementation** | Full graph structure with nodes, edges, frontier, scoring, and pruning |
| **Transformation Agents** | Explicit Generate, Aggregate, Refine agent templates with self-scoring |
| **Graph Traversal Strategy** | Defined early/mid/late depth strategies and KeepBestN pruning |
| **Controller Architecture** | GoT Controller maintains graph state and orchestrates transformations |
| **Execution Example** | Complete 4-iteration walkthrough showing GoT in action |
| **State Management** | JSON graph state saved to /RESEARCH/[topic]/graph_state.json |

**Assessment:** V2 is architecturally stronger than V1 with proper GoT mechanics. However, the core decision-making frameworks from V1 feedback were not implemented.

---

## Critical Gaps Still Remaining in V2

| Pri | Gap | Status | Required Action |
|-----|-----|--------|-----------------|
| **P0** | No stopping criteria/depth budget | ❌ NOT FIXED | Add Research Intensity Tiers classification |
| **P0** | No hypothesis-driven research | ❌ NOT FIXED | Add Phase 1.5: Hypothesis Formation |
| **P1** | No contradiction resolution protocol | ❌ NOT FIXED | Add Contradiction Triage framework |
| **P1** | Missing "So What?" synthesis | ❌ NOT FIXED | Add Implications Engine to Phase 5 |
| **P1** | No inter-agent coordination | ⚠️ PARTIAL | Enhance GoT Controller with checkpoint aggregation |
| **P2** | No disconfirmation search | ❌ NOT FIXED | Add Red Team Agent to transformation types |
| **P2** | No fast path for simple queries | ❌ NOT FIXED | Add Question Complexity Classifier (Phase 0) |
| **P2** | Missing confidence scoring for claims | ❌ NOT FIXED | Add Claim Confidence tags to output requirements |
| **P3** | No domain-specific protocols | ❌ NOT FIXED | Create domain overlay files (healthcare, financial, legal) |

---

# Implementation 1: Research Intensity Tiers (P0)

## Location in Document
Insert immediately after **Phase 1: Question Scoping**, before **Phase 2: Retrieval Planning**

## Add This Section

```markdown
### Phase 1.1: Research Intensity Classification

Before launching agents, classify the research request to set appropriate resource allocation:
```

| Tier | Trigger Conditions | Agents | Time Budget | GoT Depth |
|------|-------------------|--------|-------------|-----------|
| **Quick** | Known answer, single authoritative source | 1-2 | 10 min | Max depth: 1 |
| **Standard** | Multi-faceted, moderate complexity | 3-5 | 30-60 min | Max depth: 3 |
| **Deep** | Novel question, high stakes, conflicting evidence | 5-8 | 2-4 hours | Max depth: 4 |
| **Exhaustive** | Critical decision, requires comprehensive coverage | 8-12 | 4-8 hours | Max depth: 5+ |

## Add to GoT Controller Prompt

```
INTENSITY CLASSIFICATION (execute before graph initialization):

1. Evaluate question complexity (1-5 scale)
2. Assess decision stakes: low / medium / high / critical
3. Estimate source conflict probability: low / medium / high
4. Assign tier: Quick / Standard / Deep / Exhaustive
5. Set stopping criteria based on tier:
   - Quick: Stop when score > 7 OR depth = 1
   - Standard: Stop when score > 8 OR depth = 3
   - Deep: Stop when score > 9 OR depth = 4
   - Exhaustive: Stop when score > 9.5 OR depth = 5

6. Communicate budget constraints to all transformation agents:
   "Budget: [tier] - Max [X] agents, [Y] minutes, depth [Z]"
```

---

# Implementation 2: Hypothesis Formation (P0)

## Location in Document
Insert as **Phase 1.5** between Question Scoping (Phase 1) and Retrieval Planning (Phase 2)

## Add This Section

```markdown
### Phase 1.5: Hypothesis Formation

Transform research from information gathering into hypothesis testing:

1. Generate 3-5 testable hypotheses about the likely answer
2. Assign prior probability to each: High (70-90%) / Medium (40-70%) / Low (10-40%)
3. Design research to explicitly CONFIRM or DISCONFIRM each hypothesis
4. Track probability shifts as evidence accumulates
5. Final output reports hypothesis outcomes, not just facts
```

## Add Hypothesis Tracking to Graph State

```json
// Add to graph_state.json structure:
{
  "nodes": {...},
  "edges": [...],
  "frontier": [...],
  "hypotheses": [
    {
      "id": "H1",
      "statement": "[Testable hypothesis]",
      "prior": 0.75,
      "current": 0.75,
      "evidence_for": [],
      "evidence_against": [],
      "status": "testing"  // testing | confirmed | disconfirmed | inconclusive
    }
  ],
  "budget": {...}
}
```

## Add Hypothesis Agent Template

```
Task: "Hypothesis Generator - [Topic]"
Prompt: You are generating testable hypotheses for: "[TOPIC]"

Generate 3-5 hypotheses that:
- Are specific enough to be tested with available sources
- Cover the most likely answers to the research question
- Include at least one contrarian/unexpected hypothesis
- Can be confirmed OR disconfirmed (not vague)

For each hypothesis, specify:
1. The testable statement
2. Prior probability (with reasoning)
3. What evidence would CONFIRM it
4. What evidence would DISCONFIRM it
5. Key search queries to test it

Return:
{
  "hypotheses": [
    {
      "statement": "...",
      "prior": 0.XX,
      "prior_reasoning": "...",
      "confirming_evidence": ["..."],
      "disconfirming_evidence": ["..."],
      "test_queries": ["..."]
    }
  ]
}
```

---

# Implementation 3: Contradiction Triage Protocol (P1)

## Location in Document
Insert in **Phase 4: Source Triangulation**, under "Handle inconsistencies"

## Replace Current Text With

```markdown
### Contradiction Triage Protocol

When sources conflict, do NOT pick a winner. Apply this framework:
```

| Conflict Type | Resolution Method |
|---------------|-------------------|
| **Data Disagreement** | Numbers differ → Find primary/original source; use most recent; note the range found |
| **Interpretation Disagreement** | Experts reach different conclusions → Present both views with evidence strength; do not pick winner |
| **Methodological Disagreement** | Studies use different methods → Evaluate study quality (A-E rating); weight findings accordingly |
| **Paradigm Conflict** | Core assumptions differ → Flag as unresolved; present both paradigms; let user decide |

## Add Contradiction Resolution Agent

```
Task: "Contradiction Resolver - [Conflict ID]"
Prompt: Two or more sources disagree on: "[CONFLICTING CLAIM]"

Source A: [CLAIM_A] (Quality: [RATING])
Source B: [CLAIM_B] (Quality: [RATING])

Analyze:
1. CLASSIFY the conflict type:
   - Data disagreement (numbers differ)
   - Interpretation disagreement (conclusions differ)
   - Methodological disagreement (methods differ)
   - Paradigm conflict (assumptions differ)

2. INVESTIGATE:
   - Search for the PRIMARY source of each claim
   - Check publication dates and methodology
   - Look for meta-analyses or systematic reviews

3. DOCUMENT:
   - The specific disagreement (quote both)
   - Evidence supporting each side
   - Your confidence-weighted assessment
   - What additional info would resolve this

Return:
{
  "conflict_type": "...",
  "resolution": "...",
  "confidence": 0.XX,
  "remaining_uncertainty": "...",
  "user_decision_needed": true/false
}
```

---

# Implementation 4: Implications Engine (P1)

## Location in Document
Insert at end of **Phase 5: Knowledge Synthesis**, before Phase 6

## Add This Section

```markdown
### Implications Engine — "So What?" Analysis

For every major finding, the synthesis must answer four questions:

| Question | What It Addresses |
|----------|-------------------|
| **SO WHAT?** | Why does this finding matter? What is its significance? |
| **NOW WHAT?** | What action does this suggest? What should the user do? |
| **WHAT IF?** | What happens if this trend continues or reverses? |
| **COMPARED TO?** | How does this compare to alternatives or baselines? |

**Output Requirement:** Each section of the final report must end with an "Implications" subsection answering these questions for the user's specific context.
```

## Add to Aggregate Agent Template

```
// Add to existing Aggregate Agent prompt:

After synthesizing findings, add IMPLICATIONS section:

**SO WHAT?**
[Why this matters for the user's stated purpose]

**NOW WHAT?**
[Specific actionable recommendations]

**WHAT IF?**
[Scenario analysis: if trend continues/reverses]

**COMPARED TO?**
[Benchmarks, alternatives, historical baselines]
```

---

# Implementation 5: Checkpoint Aggregation (P1)

## Location in Document
Enhance the existing GoT Controller section under "Automatic GoT Execution Using Subagents"

## Add to GoT Controller Loop

```markdown
### Checkpoint Aggregation Protocol

After iteration 2 (approximately 50% of Phase 3), execute a coordination checkpoint:
```

```
CHECKPOINT AGGREGATION - Execute at depth=2:

1. PAUSE all transformation agents

2. COLLECT preliminary findings from all active nodes

3. ANALYZE for:
   - OVERLAP: Which agents found the same information?
     → Reassign one to new research angle
   - GAPS: What important aspects have no coverage?
     → Spawn targeted Generate agent
   - CONTRADICTIONS: Which findings conflict?
     → Deploy Contradiction Resolver agent
   - DEAD ENDS: Which agents found insufficient sources?
     → Terminate and reallocate budget
   - HYPOTHESIS UPDATE: Does new evidence shift priors?
     → Update hypothesis probabilities

4. ISSUE updated instructions to all agents:
   "CHECKPOINT UPDATE: [New priorities/redirections]"

5. RESUME graph traversal with adjusted frontier
```

Save checkpoint state to: `/RESEARCH/[topic]/checkpoint_[iteration].json`

---

# Implementation 6: Red Team Agent (P2)

## Location in Document
Add as new transformation type alongside Generate, Aggregate, Refine

## Add This Transformation

```markdown
### Red Team (Devil's Advocate) Transformation

**Purpose:** Find the strongest arguments AGAINST emerging conclusions
**Deploy:** At depth 3+ when aggregate scores exceed 8.0
**Required:** Must include output in final report as "Limitations & Counter-Evidence" section
```

## Red Team Agent Template

```
Task: "GoT Red Team - Node [ID]"
Prompt: You are the DEVIL'S ADVOCATE transformation.

Current emerging conclusions:
"[AGGREGATED_FINDINGS]"
Current aggregate score: [SCORE]

Your mission: Find evidence AGAINST these conclusions.

Search for:
1. Data that contradicts main findings
2. Case studies where this approach FAILED
3. Expert opinions that DISAGREE with consensus
4. Methodological weaknesses in cited studies
5. Edge cases where conclusions don't hold
6. Alternative explanations for the same data

Do NOT try to disprove counterarguments.
Present them at their STRONGEST.

Return:
{
  "counterarguments": [
    {
      "claim": "[Counter-claim]",
      "evidence": "[Supporting evidence]",
      "source": "[Citation]",
      "strength": "strong/moderate/weak"
    }
  ],
  "methodological_concerns": [...],
  "alternative_explanations": [...],
  "remaining_uncertainties": [...],
  "score": X.X  // Quality of counterargument analysis
}
```

## Update Graph Traversal Strategy

```
// Modify existing traversal logic:

1. Early Depth (0-2): Aggressive Generate(3) to explore
2. Mid Depth (2-3): Mix of Generate + Refine
3. Late Depth (3-4): Aggregate best branches + Refine
4. MANDATORY at depth 3+: Deploy RedTeam(1) when aggregate > 8.0  // NEW
5. Termination: Include Red Team output in final synthesis  // NEW
```

---

# Implementation 7: Question Complexity Classifier (P2)

## Location in Document
Add as **Phase 0** before Phase 1: Question Scoping

## Add This Section

```markdown
### Phase 0: Question Complexity Classification (Fast-Path Routing)

Before beginning research, classify the question to determine appropriate process depth:
```

| Type | Characteristics | Process |
|------|-----------------|---------|
| **Type A** | LOOKUP: Single fact, known authoritative source | Direct WebSearch → Answer. Skip GoT entirely. 1-2 minutes. |
| **Type B** | SYNTHESIS: Multiple facts requiring aggregation | Abbreviated GoT: 2-3 agents, depth 2 max, 15 minutes. |
| **Type C** | ANALYSIS: Requires judgment, multiple perspectives | Full 7-phase process with standard GoT. 30-60 minutes. |
| **Type D** | INVESTIGATION: Novel question, high uncertainty | Extended GoT + hypothesis testing + Red Team. 2-4 hours. |

## Classification Prompt (Execute First)

```
Before initializing GoT, classify this research request:

"[USER_QUESTION]"

Classify as:
- Type A (LOOKUP): Can be answered with a single authoritative source
- Type B (SYNTHESIS): Requires combining multiple sources
- Type C (ANALYSIS): Requires judgment and multiple perspectives
- Type D (INVESTIGATION): Novel/complex, requires hypothesis testing

Classification: [A/B/C/D]
Reasoning: [Why this classification]
Process: [Which phases to execute]
Estimated time: [X minutes/hours]
```

---

# Implementation 8: Claim Confidence Scoring (P2)

## Location in Document
Add to "Citation Requirements & Source Traceability" section, after Source Quality Ratings

## Add This Section

```markdown
### Claim Confidence Scoring

Source quality (A-E) rates the SOURCE. Claim confidence rates the CLAIM itself.
A claim from an A-rated source can still have low confidence (small sample, preliminary data).
```

| Confidence Level | Criteria for Assignment |
|------------------|------------------------|
| **HIGH (90%+)** | Multiple A/B sources agree; large sample sizes (n>1000); replicated findings across independent studies |
| **MEDIUM (60-90%)** | Single strong source OR multiple weaker sources agree; some methodological caveats; moderate sample sizes |
| **LOW (30-60%)** | Preliminary data; expert opinion without empirical backing; logical inference from adjacent data; small samples |
| **SPECULATIVE (<30%)** | Single weak source; theoretical; extrapolated; limited or no empirical support; preprint or unpublished |

## Output Format Requirement

**Every major claim must include confidence tag:**

❌ **WRONG:** 
> "Studies show metformin reduces diabetes risk."

✅ **RIGHT:** 
> "The Diabetes Prevention Program demonstrated that metformin reduces diabetes incidence by 31% over 2.8 years in high-risk individuals [HIGH CONFIDENCE: 3 RCTs, n>50,000, replicated] (Knowler et al., 2002, NEJM, PMID: 11832527)"

---

# Implementation 9: Domain-Specific Overlays (P3)

## Location in Document
Create as separate modular files that can be loaded based on research topic

---

## Healthcare Domain Overlay

**File:** `DOMAIN_OVERLAYS/healthcare.md`

```markdown
## Healthcare Research Requirements

### Mandatory Citation Elements
- PubMed ID (PMID) for all medical claims
- FDA approval status for drugs/devices
- Clinical trial phase for emerging treatments
- ClinicalTrials.gov registration number
- ICD-10 codes when discussing diagnoses

### Required Checks
- [ ] Check RetractionWatch for cited papers
- [ ] Verify FDA approval status
- [ ] Note if treatment is off-label
- [ ] Include adverse event data
- [ ] Check for conflicts of interest

### Source Priority
1. Cochrane Reviews
2. NICE/USPSTF Guidelines
3. Peer-reviewed RCTs
4. Cohort/Case-control studies
5. Expert consensus statements

### Red Flags
- Claims without PMID
- Predatory journal publications
- Industry-funded without disclosure
- Animal studies presented as human evidence
```

---

## Financial Domain Overlay

**File:** `DOMAIN_OVERLAYS/financial.md`

```markdown
## Financial Research Requirements

### Mandatory Citation Elements
- SEC filing citations (10-K, 10-Q, 8-K)
- Reporting period dates for all financials
- Bloomberg/Reuters terminal verification
- Analyst credentials for cited opinions

### Required Checks
- [ ] Verify numbers against SEC filings
- [ ] Check for restatements
- [ ] Note accounting method changes
- [ ] Include required risk disclosures
- [ ] Identify forward-looking statements

### Source Priority
1. SEC EDGAR filings
2. Company earnings calls/transcripts
3. Major wire services (Reuters, Bloomberg)
4. Institutional research (with credentials)
5. Industry publications

### Red Flags
- Numbers without filing reference
- Projections without methodology
- Analyst reports without credentials
- Unverified social media claims
```

---

## Legal Domain Overlay

**File:** `DOMAIN_OVERLAYS/legal.md`

```markdown
## Legal Research Requirements

### Mandatory Citation Elements
- Jurisdiction for all legal claims
- Case status (pending, decided, appealed)
- Statutory authority citations
- Regulatory agency references

### Required Checks
- [ ] Verify case status via Westlaw/LexisNexis
- [ ] Check for subsequent history
- [ ] Note circuit splits or conflicts
- [ ] Identify binding vs. persuasive authority
- [ ] Flag unsettled law areas

### Source Priority
1. Primary sources (statutes, regulations, cases)
2. Official agency guidance
3. Legal treatises (Restatements, etc.)
4. Law review articles
5. Practitioner publications

### Red Flags
- Overruled or superseded cases
- Dicta cited as holding
- Wrong jurisdiction applied
- Missing subsequent history
```

---

# Document Structure Recommendation

The V2 document is approximately 4,500 words—too long for effective agent context management. Split into modular files:

| File | Contents |
|------|----------|
| **CORE_PROCESS.md** | 7 phases + Phase 0 + Phase 1.5 — Maximum 600 words |
| **GOT_IMPLEMENTATION.md** | Graph structure, transformations, scoring, pruning, traversal strategy |
| **AGENT_TEMPLATES.md** | All agent prompts: Generate, Aggregate, Refine, Red Team, Hypothesis, Contradiction |
| **QUALITY_STANDARDS.md** | Citation rules, confidence scoring, verification protocols, checklist |
| **DECISION_FRAMEWORKS.md** | Intensity tiers, complexity classifier, contradiction triage, implications engine |
| **DOMAIN_OVERLAYS/** | Separate files: healthcare.md, financial.md, legal.md, market.md |
| **USER_INTERACTION.md** | Question gathering, output structure, example flows |

**Loading Strategy:** Master prompt loads CORE_PROCESS + relevant overlays + specific agent templates based on question type and domain.

---

# Implementation Roadmap

## Week 1-2: Foundation (P0 Items)

1. Add Phase 0: Question Complexity Classifier
2. Add Phase 1.1: Research Intensity Tiers
3. Add Phase 1.5: Hypothesis Formation
4. Update graph_state.json schema for hypotheses

## Week 3-4: Decision Frameworks (P1 Items)

1. Add Contradiction Triage protocol to Phase 4
2. Add Implications Engine to Phase 5
3. Implement Checkpoint Aggregation in GoT Controller
4. Update Aggregate agent template with So What analysis

## Week 5-6: Quality Enhancements (P2 Items)

1. Add Red Team transformation and agent template
2. Implement Claim Confidence scoring system
3. Update all agent templates with confidence tags
4. Update traversal strategy for mandatory Red Team at depth 3+

## Week 7-8: Specialization & Structure (P3 Items)

1. Create Healthcare domain overlay
2. Create Financial domain overlay
3. Create Legal domain overlay
4. Split document into modular files
5. Test end-to-end with all improvements

---

# Appendix A: Complete Updated Phase Structure

| Phase | Name | Key Elements |
|-------|------|--------------|
| **Phase 0** | Complexity Classification | 🆕 Type A/B/C/D routing, fast-path for simple queries |
| **Phase 1** | Question Scoping | Clarify question, define success criteria, identify constraints |
| **Phase 1.1** | Intensity Classification | 🆕 Quick/Standard/Deep/Exhaustive tier assignment |
| **Phase 1.5** | Hypothesis Formation | 🆕 Generate testable hypotheses with priors |
| **Phase 2** | Retrieval Planning | Break into subtopics, generate queries, select sources |
| **Phase 3** | Iterative Querying | ⬆️ ENHANCED — Add checkpoint aggregation at depth 2 |
| **Phase 4** | Source Triangulation | ⬆️ ENHANCED — Add contradiction triage + Red Team agent |
| **Phase 5** | Knowledge Synthesis | ⬆️ ENHANCED — Add implications engine (So What? analysis) |
| **Phase 6** | Quality Assurance | ⬆️ ENHANCED — Add claim confidence scoring |
| **Phase 7** | Output & Packaging | Format, executive summary, bibliography, export |

---

# Appendix B: Complete GoT Transformation Types

| Transformation | Purpose | When to Deploy |
|----------------|---------|----------------|
| **Generate(k)** | Create k diverse research perspectives | Early depth (0-2) for exploration |
| **Aggregate(k)** | Merge k thoughts into stronger synthesis | Mid/Late depth (2-4) for combining findings |
| **Refine(1)** | Improve existing thought quality | When score < 7 at any depth |
| **Score(1)** | Evaluate thought quality (0-10) | After every transformation |
| **KeepBestN(n)** | Prune to top n nodes per level | After scoring at each depth level |
| **RedTeam(1)** | 🆕 Find counter-evidence | Depth 3+ when aggregate > 8.0 |
| **Hypothesis()** | 🆕 Generate/update hypotheses | Phase 1.5 and at checkpoints |
| **Contradict()** | 🆕 Resolve source conflicts | Phase 4 when conflicts detected |

---

# Conclusion

Version 2 significantly improved GoT implementation mechanics. The nine additions in this document transform it from a sophisticated information-gathering system into a world-class insight-generation engine. Key shifts:

- **From unbounded to calibrated:** Intensity tiers and complexity classification control resource allocation
- **From facts to hypotheses:** Research tests beliefs, tracks probability shifts, reports conclusions
- **From ignoring to resolving:** Contradiction triage provides explicit decision framework
- **From findings to implications:** Every section answers So What? for user context
- **From confirmation to challenge:** Red Team agent ensures conclusions survive scrutiny
- **From source ratings to claim confidence:** Users can distinguish strong from weak claims

Implementing these additions will produce research outputs that are not just comprehensive and well-cited, but genuinely useful for decision-making.

---

*— End of Implementation Guide —*