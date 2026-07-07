# EDU-REQ-0201

## METADATA

| Field | Value |
|-------|-------|
| **ID** | EDU-REQ-0201 |
| **TITLE** | Student Intelligence System Models Working Memory Profile |
| **TYPE** | Outcome |
| **OWNER** | Student Intelligence System (SIS) |
| **SEVERITY** | Critical |
| **STABILITY** | Core |
| **STATUS** | Frozen |
| **VERSION** | 2.0 |
| **INTRODUCED** | Phase 3, Batch 2 (2026-07-08) |
| **LAST AMENDED** | Never amended |
| **GROUNDING LAWS** | EDU-LAW-0101 |
| **GROUNDING PRINCIPLES** | EDU-PRIN-0201 |
| **TAGS** | working-memory, cognitive-load, student-model, learning-science |

---

## LAYER 1 — NORMATIVE (Constitutional Authority)

### Constitutional Intent

Students differ in working memory capacity—the amount of information they can hold and manipulate simultaneously. A student struggling with a multi-step problem may not lack ability; they may be overwhelmed by simultaneous complexity. Adapt must estimate each student's working memory profile and use these estimates to calibrate problem complexity, pacing, and scaffolding.

Without accurate working memory estimates, Adapt cannot distinguish cognitive overload from lack of understanding, cannot calibrate appropriately to individual students, and cannot know when to scaffold and when to challenge.

### Constitutional Rationale

This requirement operationalizes EDU-PRIN-0201 (Adapt models cognitive load constraints) by establishing the minimum observable data necessary for subsequent cognitive load adjustments (EDU-REQ-0202, EDU-REQ-0204, EDU-REQ-0205).

### Constitutional Obligation

**Adapt's Student Intelligence System shall estimate each student's working memory profile by inferring capacity from performance patterns across problems of varying complexity, response time distributions, and error patterns.**

### Constitutional Invariants

- Must never assume all students have identical working memory capacity
- Must never ignore response-time data when available
- Must never attribute working memory struggles to lack of ability without evidence
- Must never assume a single low-complexity problem proves limited working memory

---

## LAYER 2 — INTERPRETIVE (Authoritative Guidance)

### Interpretation

"Working memory profile" means:
- Estimate of how much complexity the student can handle simultaneously
- Recognition that capacity varies by:
  - Problem domain (may be different for math vs. language)
  - Familiarity (familiar domains require less capacity)
  - Emotional state (anxiety reduces effective capacity)
- Dynamic, not static (improves with practice and expertise development)

Estimate from observable patterns:
- Error rate at different complexity levels
- Response time distributions
- Self-report of confusion or overwhelm
- Recovery after scaffolding

### Observable Behaviors

- SIS maintains a working memory estimate for each student for each domain
- Estimate is updated after each problem attempt
- Estimate reflects actual performance patterns (not assumptions)
- PRE uses these estimates to adjust complexity

### Audit Procedure

1. For any student with >20 problems across 3+ complexity levels
2. Retrieve SIS's working memory estimate
3. Verify: estimate correlates with performance at those complexity levels
4. Verify: higher complexity → higher error rate (or similar, if capped by working memory)
5. Verify: response time increases with complexity

### Compliance Criteria

- ✓ SIS maintains working memory profile for each student-domain pair
- ✓ Profiles are updated after each learning event
- ✓ Profiles reflect observed performance patterns
- ✓ Profiles can be retrieved and audited
- ✓ Estimates inform pedagogical decisions

### Implementation Boundary

**Owner:** Student Intelligence System (SIS)

**Scope:** All problem-solving domains where complexity varies

**Integration Points:**
- Input: Problem solutions, response times, error patterns
- Output: Used by PRE for complexity adjustment

---

### Failure Modes

**Failure:** Working memory estimates based on single error, not patterns

**Impact:** One difficult problem leads to false conclusion about capacity

**Failure:** No distinction between "overwhelmed by complexity" vs. "lacks knowledge"

**Impact:** Appropriate scaffolding withheld; misattributes working memory limits to ability

---

### Constitutional Logic Trace

```
EDU-LAW-0101 (Never mistake performance for understanding)
  ↓ requires distinguishing overload from incompetence
EDU-PRIN-0201 (Adapt models cognitive load constraints)
  ↓ operationalized through
EDU-REQ-0201 (SIS Models Working Memory Profile)
  ↓ implemented through
SIS Working Memory Estimator
  └─ Input: Problem complexity levels, response times, error patterns
  └─ Output: Capacity estimate (0-10 scale for domains)
```

---

## LAYER 3 — INFORMATIVE (Non-Normative)

### Grounding Evidence

**Working Memory Capacity:** Working memory holds ~4 chunks of information simultaneously (Miller, 1956; Cowan, 2001). Chunk size depends on expertise: experts work with larger chunks.

**Cognitive Load Theory:** Intrinsic load (inherent complexity) × Extraneous load (design) → Total load. If total load exceeds capacity, learning fails (Sweller, 1988).

---

## REPAIR RECORD

**Repair Record:** R-0011  
**Status:** FROZEN
