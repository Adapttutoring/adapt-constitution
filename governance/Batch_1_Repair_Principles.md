---
name: batch1_repair_principles
description: Explicit constraints and principles governing all Batch 1 requirement repairs
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fad3efa2-79da-4427-8f17-e607d49ee8e8
---

# BATCH 1 REPAIR PRINCIPLES

**Effective Date:** 2026-07-08  
**Authority:** Canonical Template v2.0, Enhancement 4  
**Scope:** All repairs to EDU-REQ-0101 through EDU-REQ-0110

---

## CORE REPAIR PRINCIPLES

### Principle 1: Preserve Constitutional Intent

**Rule:** Do not change what the requirement fundamentally commits Adapt to do.

**Application:**
- The constitutional obligation may be reworded for clarity and mandatory language
- Implementation details may be removed
- Research claims may be moved to Grounding Evidence
- But the core commitment must remain unchanged

**Example:**
- ✓ Original: "models student as developing learner with multidimensional outcomes"
- ✓ Repair: "Student Intelligence System shall model students as developing learners across multidimensional learning outcomes"
- ✗ Repair: "Student Intelligence System shall optimize student performance metrics" [violates intent]

---

### Principle 2: Do Not Introduce New Obligations Unless Required by Atomicity

**Rule:** Repairs add content to Template v2.0 (Invariants, Failure Modes, Logic Trace, etc.) but do not add new constitutional commitments.

**Application:**
- If a requirement is already written as a single obligation, keep it as one
- If a requirement contains multiple independent obligations, split it into separate requirements
- New requirements from splits receive new UIDs
- Original UID preserved only if the original requirement remains substantially valid

**Example of Valid Split:**
- Original EDU-REQ-0206: "Spacing System tracks and schedules retrieval" (TWO obligations)
  - Split into: EDU-REQ-0206 (Track), EDU-REQ-0207 (Schedule)
  - New UID created: EDU-REQ-0207
  - Original intent preserved across both

---

### Principle 3: Do Not Weaken Constitutional Authority

**Rule:** Repairs clarify obligations; they do not reduce them.

**Application:**
- If original says "shall", repaired version says "shall"
- If original is mandatory, repaired version remains mandatory
- If original is vague about scope, repair clarifies but does not narrow scope

**Example:**
- ✓ Original: "identifies stakeholder tensions" → Repair: "shall identify when stakeholder values conflict and log the conflict"
- ✗ Repair: "shall identify major stakeholder tensions when detected" [weakens by narrowing scope]

---

### Principle 4: Remove Implementation Detail Without Changing Obligation

**Rule:** Implementation specifics move to Implementation Notes or Grounding Evidence; obligation remains the same.

**Application:**
- Parameters (percentages, time windows, thresholds) move to Implementation Notes
- Research claims and effect sizes move to Grounding Evidence
- Specific algorithms or UI decisions move to Implementation Notes
- Constitutional obligation states WHAT must happen, not HOW

**Example:**
- Original: "feedback timing varies by goal"
- ✗ Repair: "feedback is immediate for procedures, delayed for concepts" [still implementation detail]
- ✓ Repair: "shall provide feedback that is corrective and diagnostically targeted"
- Implementation Note: "Timing varies by goal (immediate for procedures, delayed for concepts); tunable"

---

### Principle 5: Preserve UID Permanence

**Rule:** Once a UID is assigned, it is never reused or changed.

**Application:**
- If a requirement is revised (not split), it keeps its original UID
- If a requirement is split, the primary obligation keeps the original UID; new obligations get new UIDs
- If a requirement is deprecated, its UID is marked deprecated but never reassigned
- Traceability records document all UID changes

**Example:**
- EDU-REQ-0101 revised → still EDU-REQ-0101
- EDU-REQ-0101 split into two → EDU-REQ-0101 (primary) + EDU-REQ-0111 (new)
- EDU-REQ-0101 deprecated → marked deprecated; UID never used again

---

### Principle 6: Preserve Traceability

**Rule:** Every repaired requirement traces back to original evidence and forward to implementation.

**Application:**
- Constitutional Logic Trace section shows: LAW → PRIN → REQ → Implementation
- Repair Log documents every change and its justification
- Related Requirements section identifies dependencies
- Traceability is bidirectional (what requires this? what does this enable?)

---

### Principle 7: Record Every Substantive Change

**Rule:** No silent modifications. Every change ≥ wording is logged with reason.

**Application:**
- Punctuation changes: not logged
- Wording clarification that preserves meaning: logged with "wording clarification"
- Added sections (Invariants, Failure Modes, etc.): logged with "Template v2.0 compliance"
- Removed implementation details: logged with "Implementation separation" and location
- Split into multiple requirements: logged with "Atomicity enforcement"

**Repair Log Format:**
```
REPAIR RECORD R-0001

Requirement: EDU-REQ-0101
Type: Revision
Change: Added Constitutional Invariants section
Reason: Template v2.0 compliance
Original Text: [preserved from evidence]
Repaired Text: [new version]
Traceability: Batch_1_Original_Evidence.md → Batch_1_Repair_Input_v2.0.md → Batch_1_Repair_Log.md
Date: 2026-07-08
```

---

### Principle 8: Ensure Repaired Requirements Conform to Template v2.0

**Rule:** Every requirement must include all mandatory Template v2.0 sections after repair.

**Application:**
- METADATA block (ID, Title, Type, Owner, Severity, Stability, Status, Version, etc.)
- LAYER 1 — NORMATIVE (Constitutional Intent, Constitutional Rationale, Constitutional Obligation, Constitutional Invariants)
- LAYER 2 — INTERPRETIVE (Interpretation, Observable Behaviors, Audit Procedure, Compliance Criteria, Implementation Boundary, Failure Modes, Constitutional Logic Trace)
- LAYER 3 — INFORMATIVE (Grounding Evidence, Implementation Notes, Depends On, Enables, Potential Tensions, Downstream Dependencies)

**Gate Enforcement:** Every repaired requirement must pass all six gates before freezing.

---

## EXECUTION CONSTRAINTS

### Constraint 1: Linear Repair Order

Repair EDU-REQ-0101 through EDU-REQ-0110 in sequence.

Do not skip ahead or repair out of order.

This ensures consistency and catches patterns early.

### Constraint 2: Single Repair Per Cycle

Repair one requirement completely:
1. Read original from evidence
2. Identify atomicity violations
3. Plan split if necessary
4. Write repaired version(s)
5. Run six gates
6. Run Constitutional Regression Review
7. Freeze requirement
8. Move to next

Do not batch multiple requirements before running gates.

### Constraint 3: Immutable Evidence

Never edit Batch_1_Original_Evidence.md.

All repairs reference it but do not modify it.

If a repair requires clarification of original intent, escalate rather than inferring.

### Constraint 4: Complete Audit Trail

Every modification must be traceable:
- Original → Evidence file
- Evidence → Repair Input
- Repair Input → Repair Log + Repaired version
- Repaired version → Six Gates result
- Six Gates result → Freeze record

No step is skipped; no audit trail is incomplete.

---

## RATIONALE

These principles ensure that Batch 1 repairs:
- Preserve constitutional authority
- Maintain complete traceability
- Enable future audits
- Prevent drift from original intent
- Support long-term constitutional stability

**Authority:** This document is part of the permanent Batch 1 repair governance.

**Status:** LOCKED — Principles do not change during repairs

**Modification:** Only through formal constitutional amendment
