# EDU-REQ-0101

## METADATA

| Field | Value |
|-------|-------|
| **ID** | EDU-REQ-0101 |
| **TITLE** | Student Intelligence System Models Developing Learners |
| **TYPE** | Outcome |
| **OWNER** | Student Intelligence System (SIS) |
| **SEVERITY** | Critical |
| **STABILITY** | Core |
| **STATUS** | Frozen |
| **VERSION** | 2.0 |
| **INTRODUCED** | Phase 3, Batch 1 (2026-07-08) |
| **LAST AMENDED** | Never amended |
| **GROUNDING LAWS** | EDU-LAW-0101, EDU-LAW-0107 |
| **GROUNDING PRINCIPLES** | EDU-PRIN-0101 |
| **TAGS** | student-model, multidimensional-outcomes, learning-science, foundation |

---

## LAYER 1 — NORMATIVE (Constitutional Authority)

### Constitutional Intent

Students are developing learners. Understanding them requires measuring more than test scores or speed. Adapt must model the complete scope of educational outcomes that matter: how deeply students understand, how long they retain knowledge, whether they can transfer to new contexts, how aware they are of their own learning, whether they find learning intrinsically motivating, whether they are confidently calibrated, whether they are becoming independent learners, and whether their wellbeing is preserved throughout learning.

Without a multidimensional student model, Adapt cannot distinguish genuine learning from performance illusion, cannot recognize when short-term success masks long-term confusion, and cannot optimize for outcomes that matter beyond the next exam.

This requirement operationalizes EDU-LAW-0101 (never mistake performance for understanding) and EDU-LAW-0107 (prioritize long-term learning) by establishing the foundational infrastructure that enables all subsequent educational judgments.

### Constitutional Rationale

This requirement operationalizes EDU-PRIN-0101 (Adapt models students as developing learners across multidimensional outcomes) by establishing the minimum constitutional commitment necessary for:
- Retention estimation (required by EDU-REQ-0206, spacing system)
- Error diagnosis (required by EDU-REQ-0209, misconception analysis)
- Progress reporting (required by EDU-REQ-0104)
- Transfer assessment (required by EDU-REQ-0212)
- Metacognitive monitoring (required by EDU-REQ-0213)

### Constitutional Obligation

**Adapt's Student Intelligence System shall model each student as a developing learner across multidimensional educational outcomes: Understanding depth, Long-term Retention, Transfer capability, Metacognitive Accuracy, Intrinsic Motivation, Calibrated Confidence, Learning Independence, and Wellbeing.**

### Constitutional Invariants

- Must never reduce student model to performance metrics alone
- Must never conflate test score with understanding
- Must never assume performance on one task predicts performance on transfer tasks
- Must never treat motivation, confidence, or wellbeing as secondary metrics
- Must never lose visibility of dimensions of learning deemed "hard to measure"
- Must never default to single-dimension optimization

---

## LAYER 2 — INTERPRETIVE (Authoritative Guidance)

### Interpretation

A "developing learner" model means:
- Students are not static (they change; learning is a trajectory)
- Different students develop along different trajectories
- All eight outcome dimensions matter; no dimension can be ignored
- The model estimates current state of each student on each dimension
- The model informs every subsequent pedagogical decision

"Multidimensional outcomes" are defined precisely:
- **Understanding (Conceptual Depth):** Grasping principles, relationships, and applicability conditions—not memorization
- **Retention (Long-Term Memory):** Knowledge persisting and accessible weeks, months, years later
- **Transfer (Far and Near):** Applying knowledge in new contexts; distinguishing when principles apply vs. don't
- **Metacognition (Self-Awareness):** Student awareness of what they know, don't know, and why; accuracy of self-assessment
- **Intrinsic Motivation:** Engaging with content because it's interesting/valued, not for external rewards
- **Calibrated Confidence:** Confidence correlated with actual performance; neither overconfident nor underconfident
- **Learning Independence:** Ability to learn new material without scaffolding; willingness to struggle productively
- **Wellbeing:** Psychological safety, absence of debilitating anxiety, sense of progress and agency

The SIS estimates these for each student on each concept/skill. These estimates inform:
- What to teach next (spacing and retrieval scheduling)
- How to teach it (scaffolding level, challenge calibration)
- How to communicate progress (what stories does each student need to hear?)
- When to intervene (where is this student struggling dangerously?)

### Observable Behaviors

If this requirement is implemented:
- System maintains profiles for each student showing estimated status on each of 8 outcome dimensions
- Profiles are updated after each learning event (problem attempt, assessment, etc.)
- Different students with identical test scores may have different profiles (e.g., one understands deeply, other memorized)
- System can distinguish "student got answer right via guessing" from "student understood and applied principle"
- System tracks whether a student's confidence matches their actual performance
- System records not just success/failure but type of error (which misconception led to this mistake?)
- Reports available to system stakeholders (educator, student, parent) show all 8 dimensions

### Audit Procedure

To verify this requirement was implemented:

1. **Profile Existence:** For any student with >10 learning events, retrieve their student model
   - Verify: 8 outcome dimensions present
   - Verify: each dimension has a numerical estimate (0-100 scale or equivalent)
   - Verify: timestamp showing when last updated

2. **Profile Updates:** For same student, compare profile before and after one learning event
   - Verify: at least one dimension changed
   - Verify: change is directional and reasonable (e.g., success on hard problem → increase understanding estimate)
   - Verify: change can be traced to the specific learning event

3. **Differentiation:** Compare two students with same test scores
   - Verify: their profiles differ on other dimensions
   - Verify: differences could explain different pedagogical decisions (e.g., one needs confidence-building, other needs challenge)

4. **Long-Tail Visibility:** Verify "hard to measure" dimensions are tracked
   - Verify: metacognitive accuracy has estimates (not just blank)
   - Verify: wellbeing has estimates (not marked "not available")
   - Verify: motivation has estimates (not dropped because "hard to measure")

### Compliance Criteria

- ✓ Student model schema includes all 8 outcome dimensions
- ✓ Schema stores numerical estimate for each dimension for each student-concept pair
- ✓ System updates dimensions after each learning event
- ✓ Updates are reasonable (success → increase; failure → decrease; or refinement if dimension unchanged)
- ✓ Two students with identical scores can have different profiles
- ✓ Profile changes are auditable (can trace change to triggering event)
- ✓ All 8 dimensions are visible in system outputs (not hidden or marked "not available")
- ✓ Dimension definitions are documented and used consistently

### Implementation Boundary

**Owner:** Student Intelligence System (SIS)

**Scope:** Applies to all learning contexts
- Problem solving (practice, retrieval, transfer problems)
- Assessments (formative, summative, diagnostic)
- Self-reported feedback (confidence ratings, motivation surveys)
- Long-term tracking (yearlong learning trajectories)

**Exclusions:** SIS does not dictate how other systems use the model
- PRE (Pedagogical Reasoning Engine) decides how to act on model
- Question Generation System decides what problems to generate
- Reporting System decides how to display model to different stakeholders

**Integration Points:**
- Input: Learning Events (from problem-solving systems, assessments)
- Output: Used by PRE, Error Analysis System, Progress Reporting System, Assessment System

### Failure Modes

**Failure:** Student model includes only test scores and problem-solving speed

**Impact:** System becomes unable to distinguish:
- Deep understanding from lucky guessing
- Genuine retention from recent memorization
- Transfer capability from context-specific performance
- Calibrated confidence from unwarranted confidence
- Learning independence from dependence on scaffolding
**Consequence:** Violates EDU-LAW-0101 (mistakes performance for understanding); entire foundation crumbles

---

**Failure:** Multidimensional model exists but one dimension is always "not available"

**Impact:** System blindspots. Example: if Wellbeing is never tracked:
- System cannot detect when learning is building anxiety
- Cannot notice when challenge is degrading confidence
- Cannot catch when student is in distress despite performance metrics
**Consequence:** Violates EDU-LAW-0108 (respect agency); educational debt accumulates

---

**Failure:** Model is updated inconsistently (some dimensions daily, others never)

**Impact:** Stale data creates illusions. Example: if Motivation is last updated 3 months ago:
- System recommends engagement strategies based on 3-month-old data
- Student's actual current motivation is invisible
- Pedagogical decisions become unreliable
**Consequence:** Violates EDU-LAW-0104 (decide on incomplete data)

---

### Constitutional Logic Trace

```
EDU-LAW-0101 (Never mistake performance for understanding)
  ↓ requires accurate multidimensional assessment
EDU-LAW-0107 (Prioritize long-term learning)
  ↓ requires tracking long-term retention and transfer
EDU-PRIN-0101 (Model students as developing learners)
  ↓ operationalized through
EDU-REQ-0101 (Student Intelligence System Models Developing Learners)
  ↓ implemented through
SIS Student Profile Schema
  ↓ realized in
PostgreSQL student_outcomes table
  ├─ outcome_dimension (Understanding, Retention, Transfer, etc.)
  ├─ student_id
  ├─ concept_id
  ├─ estimated_strength (0-100)
  └─ last_updated_timestamp
```

---

## LAYER 3 — INFORMATIVE (Non-Normative)

### Grounding Evidence

**Multidimensional Learning Outcomes:**
Educational research distinguishes multiple, partially independent learning outcomes. A student might memorize facts (retention) without understanding principles (understanding), or understand principles without recognizing when they apply (transfer). Bloom's taxonomy, revised (Anderson & Krathwohl, 2001), distinguishes cognitive levels. Depth of processing theory (Craik & Lockhart, 1972) predicts that understanding correlates with retention more reliably than rote memorization.

**Metacognition & Calibration:**
Calibration accuracy (match between confidence and performance) predicts long-term learning success. Dunning-Kruger effect (Dunning & Kruger, 1999) shows that low-performing learners often have high, unjustified confidence. Metacognitive training can improve calibration (Schraw, 2020s).

**Transfer:**
Near Transfer (application in similar context) and Far Transfer (application in different domain) require different instructional approaches. Interleaving improves transfer over blocked practice (Rohrer & Taylor, 2007; d≈0.8-1.0).

**Motivation & Wellbeing:**
Intrinsic motivation is more predictive of long-term engagement than external rewards (Ryan & Deci, 2000). Learning anxiety affects performance, especially for high-stakes assessments (Spielberger & Vagg, 1995). Psychological safety predicts productive struggle (Edmondson, 1999).

**Independence:**
Gradual release of responsibility (Pearson & Gallagher, 1983) models the transition from dependence on scaffolding to independence. Overly-supported learners fail to develop independent problem-solving (learned helplessness; Seligman, 1975).

### Implementation Notes

**Student Profile Representation:**

Option A (Simple): Single table with rows for each (student, concept, outcome_dimension) triple
- Advantage: Simple queries, easy to understand
- Disadvantage: High cardinality (millions of rows)

Option B (Embedded): Nested structure (student → concept → {8-dimensional vector})
- Advantage: Compact, efficient queries
- Disadvantage: Harder to aggregate across dimensions

Recommend Option B with denormalized views for reporting.

**Estimation Algorithm:**

Initial approach: Bayesian update based on performance
- Prior: weak (assume neutral starting point)
- Likelihood: problem outcome, problem difficulty, student response time
- Posterior: updated estimate after each event

Future refinement: Multi-task learning across dimensions (correlation structures)
- Observation: retention and transfer correlate (both indicate deep understanding)
- Leverage: improve estimates on one dimension using signals from others

**Staleness Policy:**

Avoid reporting estimates older than 30 days for active students. If student hasn't engaged in 30+ days:
- Retain estimates but mark as "stale"
- Do not use stale estimates for adaptive scheduling
- Flag for educator: "Last learning activity 45 days ago"

### Depends On

- **EDU-LAW-0101, EDU-LAW-0107:** Foundational laws this operationalizes
- **EDU-PRIN-0101:** Foundational principle this implements
- **Constitutional Glossary:** Definitions of all 8 outcome dimensions

### Enables

- **EDU-REQ-0102:** PRE depends on SIS model to make pedagogical decisions
- **EDU-REQ-0104:** Progress Reporting depends on SIS model to generate reports
- **EDU-REQ-0206:** Spacing System depends on retention estimates
- **EDU-REQ-0209:** Error Analysis depends on SIS tracking for misconception diagnosis
- **EDU-REQ-0212:** Transfer Assessment depends on transfer capability estimates

### Potential Tensions

**Tension 1: Measurement vs. Measurement Burden**
- Tracking 8 dimensions requires more data collection than 1-dimensional models
- More data collection may burden students (surveys, assessments)
- **Resolution:** Infer dimensions from problem-solving behavior when possible; add direct measurement (surveys) only when behavioral inference insufficient. Balance completeness with cognitive load.

**Tension 2: Precision vs. Interpretability**
- Complex multidimensional models maximize prediction but are harder to explain to students/parents
- **Resolution:** Maintain both: precise internal model for system decisions; simplified 3-4 dimension reporting to stakeholders (understand/retain, motivation, confidence)

**Tension 3: Individual Dimensions vs. Holistic Learning**
- Treating dimensions independently risks missing gestalt (how do 8 dimensions combine?)
- **Resolution:** Track individual dimensions separately but provide holistic reports that show relationships (e.g., "high understanding + low confidence" suggests calibration gap)

### Downstream Dependencies

- **Pedagogical Reasoning Engine** — uses student model to decide what to teach, how to teach it
- **Problem Generation System** — uses retention estimates to schedule retrieval practice
- **Error Analysis System** — uses learning event history and outcomes to diagnose misconceptions
- **Assessment System** — uses student model to calibrate assessment difficulty and predict exam readiness
- **Progress Reporting System** — uses student model to generate reports for students, parents, educators
- **Intervention System** — uses wellbeing/motivation/confidence estimates to flag students needing support

---

## REPAIR RECORD

**Repair Record:** R-0001  
**Date:** 2026-07-08  
**Source Evidence:** Batch_1_Original_Evidence.md, EDU-REQ-0101  
**Changes:**
- Converted from brief description to full Template v2.0
- Added Constitutional Intent and Rationale (preserves original meaning, explains why)
- Clarified Constitutional Obligation (mandatory language, no implementation detail)
- Added Constitutional Invariants (guardrails)
- Added Interpretation (what multidimensional outcomes mean)
- Added Observable Behaviors (how to verify implementation)
- Added Audit Procedure (step-by-step compliance verification)
- Added Compliance Criteria (checklist for done)
- Added Implementation Boundary (scope)
- Added Failure Modes (what could go wrong)
- Added Constitutional Logic Trace (derivation from law → principle → requirement)
- Added Grounding Evidence (research supporting this requirement)
- Added Implementation Notes (engineering guidance)
- Moved all implementation details out of obligation into Implementation Notes
- Moved research citations into Grounding Evidence

**No atomicity violations detected:** Original obligation is single and indivisible.

**Gate 1 (Template Conformance):** PASS ✓  
**Gate 2 (Atomicity & Language):** PASS ✓  
**Gate 3 (Verification Feasibility):** PASS ✓  
**Gate 4 (Glossary Consistency):** PASS ✓  
**Gate 5 (Traceability & Overlap):** PASS ✓  
**Gate 6 (Batch Approval):** PASS ✓  

**Constitutional Regression Review:** No new patterns discovered. Template v2.0 is working well.

**Status:** FROZEN
