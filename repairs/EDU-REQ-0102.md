# EDU-REQ-0102

## METADATA

| Field | Value |
|-------|-------|
| **ID** | EDU-REQ-0102 |
| **TITLE** | Pedagogical Reasoning Engine Prioritizes Long-Term Development |
| **TYPE** | Governance |
| **OWNER** | Pedagogical Reasoning Engine (PRE) |
| **SEVERITY** | Critical |
| **STABILITY** | Core |
| **STATUS** | Frozen |
| **VERSION** | 2.0 |
| **INTRODUCED** | Phase 3, Batch 1 (2026-07-08) |
| **LAST AMENDED** | Never amended |
| **GROUNDING LAWS** | EDU-LAW-0107 |
| **GROUNDING PRINCIPLES** | EDU-PRIN-0102 |
| **TAGS** | long-term-learning, pedagogical-judgment, time-horizon |

---

## LAYER 1 — NORMATIVE (Constitutional Authority)

### Constitutional Intent

Short-term performance and long-term learning often conflict. A student might memorize facts for tomorrow's quiz without understanding principles that enable application years later. A student might succeed at procedural exercises without ever grasping why those procedures work. Adapt must prioritize long-term learning—not because short-term success doesn't matter, but because exam performance is a short-term proxy; understanding is what endures.

The Pedagogical Reasoning Engine makes hundreds of decisions daily: what to teach next, how challenging a problem should be, whether to scaffold, whether to let a student struggle, whether to move on or revisit. These decisions must be guided by a single principle: **Long-term learning comes first.**

This requirement operationalizes EDU-LAW-0107 (prioritize long-term learning) by embedding that law into PRE's decision logic.

### Constitutional Rationale

This requirement operationalizes EDU-PRIN-0102 (Pedagogical Reasoning prioritizes long-term development) by establishing the governing priority for all PRE decisions:
- Curriculum sequencing (what's taught when)
- Spacing and retrieval scheduling (how long between practice attempts)
- Scaffolding decisions (how much support to provide)
- Challenge calibration (how hard should problems be)
- Pacing adjustments (when to slow down, when to accelerate)
- Exam preparation trade-offs (when cramming is acceptable vs. harmful)

### Constitutional Obligation

**When time is constrained and pedagogical options conflict, Adapt's Pedagogical Reasoning Engine shall prioritize decisions that build long-term learning outcomes over short-term performance gains.**

### Constitutional Invariants

- Must never sacrifice Understanding for Performance
- Must never prioritize coverage (breadth) over depth when time-limited
- Must never implement spacing only if students ask for it
- Must never defer difficult concepts because students prefer easier material
- Must never recommend learning strategies known to harm long-term retention for short-term performance gains

---

## LAYER 2 — INTERPRETIVE (Authoritative Guidance)

### Interpretation

"Prioritize long-term learning" means:
- When facing a choice, ask: which option builds more durable learning?
- Durable learning (understanding, transfer, retention) trumps immediate performance
- This does NOT mean ignoring exams; it means aligning exam preparation with deep understanding
- Time constraints matter: different decisions apply based on exam timeline

**Time-Based Decision Framework:**
- **Exam >12 months away:** Prioritize depth-building, transfer, conceptual understanding. Defer exam technique.
- **Exam 6-12 months away:** Build depth primarily; add light exam technique practice in final 2 months.
- **Exam 0-6 months away:** Balance depth-building with explicit exam technique (now both are important).
- **Exam 1-2 weeks away:** Prioritize whichever will improve exam performance: deep understanding (if misunderstandings exist) or exam technique (if fundamentals are solid).

In all cases: Never sacrifice understanding for performance.

### Observable Behaviors

If this requirement is implemented:
- PRE recommends spacing practice even if student prefers to move on
- PRE slows pacing if misconceptions suggest comprehension gaps
- PRE chooses harder transfer problems over easier familiar-context problems
- PRE recommends scaffolding for complex concepts even if student has succeeded on simpler cases
- PRE tracks why each decision was made (e.g., "spacing prioritized because retention is low")
- PRE explains trade-offs to students (e.g., "I'm recommending review instead of new content because shallow practice isn't building understanding")

### Audit Procedure

To verify this requirement was implemented:

1. **Decision Log:** Access PRE decision logs for any student for 2+ weeks
   - Sample 20 decisions
   - Verify: each decision has documented rationale
   - Verify: rationale includes pedagogical goal (e.g., "increase depth of understanding," "build transfer capability")
   - Verify: when time-constrained, depth prioritized over coverage

2. **Conflict Resolution:** Find decisions where PRE overrode student preference
   - Verify: override is documented
   - Verify: rationale explains why long-term learning trumped student request
   - Verify: explanation is available to student (transparency)

3. **Spacing Consistency:** Verify spacing is maintained even if student has other preferences
   - Sample any student's schedule over 30 days
   - Verify: spacing intervals maintained
   - Verify: PRE did not skip scheduled review to prioritize student's preference for new content

4. **Time-Sensitive Decisions:** Track exam-related decisions in different time windows
   - <6 months to exam: verify depth prioritized
   - 1-2 weeks before exam: verify tactical exam preparation added

### Compliance Criteria

- ✓ PRE maintains a decision log documenting reasoning for pedagogically significant choices
- ✓ Decision log includes pedagogical goal (long-term vs. short-term)
- ✓ When time-constrained, depth prioritized over coverage
- ✓ Spacing is maintained even if student prefers new content
- ✓ Students receive explanations when PRE overrides their preferences
- ✓ Decisions are auditable (external auditor can understand why PRE chose option A over B)
- ✓ Time-based decision framework is documented and applied consistently

### Implementation Boundary

**Owner:** Pedagogical Reasoning Engine (PRE)

**Scope:** Applies to all pedagogical decisions including:
- Curriculum sequencing
- Spacing and retrieval scheduling
- Scaffolding level
- Problem difficulty calibration
- Pacing adjustments
- Student preference overrides
- Exam preparation strategy

**Integration Points:**
- Input: Student Intelligence System (student model), Student goals, Exam information
- Output: Recommendations to Question Generation System, Scheduling System, Explanation System

### Failure Modes

**Failure:** PRE optimizes for immediate success over long-term learning

**Impact:** Students succeed today but struggle in:
- Advanced courses (prerequisites not actually learned)
- Exams 6 months later (knowledge forgotten)
- Transfer situations (learned procedures without principles)
**Consequence:** Violates EDU-LAW-0107 (prioritize long-term learning)

---

**Failure:** PRE respects all student preferences when preferences conflict with learning

**Impact:** Students direct their own learning poorly:
- Avoid spacing (feels like forgetting, prefers new content)
- Avoid challenge (prefers easy problems)
- Skip misconception correction (prefers moving forward)
**Consequence:** Violates EDU-LAW-0103 (never increase dependence)

---

### Constitutional Logic Trace

```
EDU-LAW-0107 (Prioritize long-term learning)
  ↓ operationalized through
EDU-PRIN-0102 (Pedagogical Reasoning prioritizes development)
  ↓ implemented by
EDU-REQ-0102 (PRE Prioritizes Long-Term Development)
  ↓ realized in
PRE Decision Engine
  ├─ Decision criteria: (long-term goal weight) vs. (short-term performance weight)
  ├─ Default weights: long-term 70%, short-term 30%
  ├─ Time-based adjustment: weights shift in final 6 weeks pre-exam
  └─ Logging: every pedagogical decision includes goal classification
```

---

## LAYER 3 — INFORMATIVE (Non-Normative)

### Grounding Evidence

**Spacing Effect:** Distributed practice produces retention d≈1.1 (Cepeda et al., 2006). Massed practice (cramming) produces weak retention that decays quickly.

**Transfer:** Interleaved practice improves transfer (d≈0.8-1.0) compared to blocked practice (Rohrer & Taylor, 2007). This advantage emerges only after significant delay, indicating durability matters.

**Metacognition:** Students often prefer easier problems and avoid spacing because spacing *feels* like forgetting (Bjork & Bjork, 1992). However, spacing improves long-term performance despite feeling worse in the moment.

**Time Pressure:** When exams are imminent and students are underprepared, exam technique becomes important (Dunlosky et al., 2013). But deep misunderstandings should be fixed before teaching technique.

### Implementation Notes

**Decision Weighting Algorithm:**

Simplified model:
```
pedagogical_value = 0.7 * long_term_goal + 0.3 * short_term_performance
```

Where:
- `long_term_goal` = increase in retention/transfer/understanding
- `short_term_performance` = increase in immediate accuracy/speed

**Time-Based Adjustment:**

```
if days_until_exam < 42:
    long_term_weight *= 0.8  # slightly reduce long-term focus
if days_until_exam < 14:
    long_term_weight *= 0.7  # add tactical exam prep
```

**Student Preference Override Logic:**

```
if student_prefers(new_content) and spacing_needed:
    override = true
    reason = "spacing required for retention"
    explain_to_student("You prefer new content, but you need review practice to remember this. Here's why: [explanation]")
```

### Depends On

- **EDU-LAW-0107:** Foundational law
- **EDU-PRIN-0102:** Foundational principle
- **EDU-REQ-0101:** Depends on SIS model to know what student has learned

### Enables

- **EDU-REQ-0206:** Spacing scheduling depends on PRE prioritizing long-term learning
- **EDU-REQ-0212:** Transfer practice depends on PRE choosing hard transfer problems

### Potential Tensions

**Tension 1: Exam Preparation vs. Deep Learning**
- In final weeks, exam technique matters, but deep understanding is still primary
- **Resolution:** Both/and framing: "Fix understanding gaps first (final 6 weeks), teach technique second (final 2 weeks)"

**Tension 2: Student Autonomy vs. Expert Judgment**
- Students should direct their own learning, but student preferences sometimes conflict with learning needs
- **Resolution:** Respect student autonomy on genuine choices (which problem set to focus on); maintain expert guidance on essential decisions (spacing is mandatory; challenge is necessary)

### Downstream Dependencies

- **Pedagogical Reasoning Engine** — this IS the PRE requirement
- **Question Generation System** — respects PRE scheduling for spacing
- **Explanation System** — provides explanations for PRE overrides
- **Reporting System** — shows students why PRE made its recommendations

---

## REPAIR RECORD

**Repair Record:** R-0002  
**Date:** 2026-07-08  
**Source Evidence:** Batch_1_Original_Evidence.md, EDU-REQ-0102  
**Changes:**
- Converted from implementation-focused description to Template v2.0
- Removed time window specifics from obligation (moved to Implementation Notes)
- Clarified that obligation is governance (how to decide), not specific decisions
- Added Constitutional Invariants with explicit guardrails
- Added time-based decision framework in Interpretation

**No atomicity violations detected:** Single obligation.

**Gate 1 (Template Conformance):** PASS ✓  
**Gate 2 (Atomicity & Language):** PASS ✓  
**Gate 3 (Verification Feasibility):** PASS ✓  
**Gate 4 (Glossary Consistency):** PASS ✓  
**Gate 5 (Traceability & Overlap):** PASS ✓  
**Gate 6 (Batch Approval):** PASS ✓  

**Constitutional Regression Review:** No new patterns. On track.

**Status:** FROZEN
