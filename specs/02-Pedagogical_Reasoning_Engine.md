# PEDAGOGICAL REASONING ENGINE (PRE) — IMPLEMENTATION SPECIFICATION

**Document ID:** SPEC-PRE-001  
**Constitutional Grounding:** EDU-REQ-0102, EDU-REQ-0202, EDU-REQ-0203, EDU-REQ-0204, EDU-REQ-0108, EDU-REQ-0110  
**Version:** 1.0  
**Status:** READY FOR IMPLEMENTATION

---

## EXECUTIVE SUMMARY

The Pedagogical Reasoning Engine is Adapt's decision-making system. It receives a student profile from SIS and makes hundreds of daily decisions:

- **What to teach next?** (curriculum sequencing)
- **How challenging should it be?** (complexity calibration)
- **How much scaffolding?** (support level)
- **When to schedule retrieval?** (spacing)
- **Should I override the student's preference?** (conflict resolution)
- **Should I escalate to human?** (decision authority)

Every decision must prioritize long-term learning over short-term performance, respect student agency, and be explainable.

---

## CONSTITUTIONAL REQUIREMENTS

| Requirement | Obligation | PRE Impact |
|---|---|---|
| EDU-REQ-0102 | Prioritize long-term development over short-term performance | Time-horizon weighting in decision algorithm |
| EDU-REQ-0202 | Adjust complexity based on cognitive load indicators | Detect overload; proactively reduce complexity |
| EDU-REQ-0203 | Adjust pacing based on processing speed | Extend time for slower processors |
| EDU-REQ-0204 | Use scaffolding for complex concepts | Explicit scaffolding management |
| EDU-REQ-0108 | Identify and resolve stakeholder conflicts | Conflict detection and resolution |
| EDU-REQ-0110 | Advocate for student with transparent justification | Override student preference with explanation |

**Observable Behaviors (from Constitution):**
- PRE recommends spacing even if student prefers new content
- PRE slows pacing if misconceptions suggest comprehension gaps
- PRE chooses harder transfer problems over easier familiar problems
- PRE recommends scaffolding even for students who succeeded on simpler cases
- PRE tracks why each decision was made
- PRE explains trade-offs to students

---

## SYSTEM ARCHITECTURE

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│        PEDAGOGICAL REASONING ENGINE (PRE)               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  INPUT: Student Profile from SIS                        │
│  ├─ 8-dimensional outcome estimates                     │
│  ├─ Working memory capacity                             │
│  ├─ Identified misconceptions                           │
│  └─ Confidence calibration                              │
│                                                         │
│  INPUT: Context                                         │
│  ├─ Time until next exam                                │
│  ├─ Current curriculum position                         │
│  ├─ Student goal/preference                             │
│  └─ System status (spacing queue, etc.)                 │
│                                                         │
│         ┌────────────────────────────────────┐           │
│         │  DECISION REASONER                 │           │
│         │                                    │           │
│         │  [Decision Tree / Rule Engine]     │           │
│         │  [Evaluates multiple options]      │           │
│         │  [Weights long-term vs. short-term]│           │
│         │                                    │           │
│         └────────────────────────────────────┘           │
│           ▲              ▲              ▲               │
│           │              │              │               │
│         ┌─┴──┬───────┬──┴──┬──────┬───┴──┬──────┐      │
│         │    │       │     │      │      │      │      │
│    Curriculum │   Complexity Pacing Scaffolding │      │
│    Sequencer  │   Optimizer Adjuster Manager  Override │
│               │       │     │      │      │      │      │
│         └─────┴───────┴─────┴──────┴──────┴──────┘      │
│                                                         │
│  OUTPUT: Pedagogical Decision                          │
│  ├─ Recommended concept to teach                        │
│  ├─ Complexity level (1-10)                             │
│  ├─ Problem type (new/retrieval/transfer)               │
│  ├─ Scaffolding level                                   │
│  ├─ Time allocation                                     │
│  ├─ Pacing adjustment                                   │
│  ├─ Conflict detection & resolution                     │
│  ├─ Reasoning explanation                              │
│  └─ Confidence in recommendation                        │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
    Problem Generation System
    (creates specific problems)
```

### Component Breakdown

| Component | Purpose | Algorithm |
|-----------|---------|-----------|
| **Curriculum Sequencer** | Determine what to teach next | Prerequisite graph + mastery level |
| **Complexity Optimizer** | Determine appropriate challenge level | Student profile + error analysis |
| **Pacing Adjuster** | Adjust time based on processing speed | Response time patterns |
| **Scaffolding Manager** | Determine support level | Concept complexity + student readiness |
| **Conflict Resolver** | Handle student preference conflicts | Priority ranking (wellbeing > learning > performance) |
| **Decision Reasoner** | Overall decision logic | Multi-objective optimization |

---

## DECISION MODEL

### Core Decision Types

**Decision Type 1: Curriculum Sequencing**
- Input: Current concept, student mastery levels
- Output: Next recommended concept
- Time Horizon: Long-term (weeks/months)
- Priority: Long-term learning

**Decision Type 2: Complexity Selection**
- Input: Student profile, misconceptions, working memory
- Output: Complexity level (1-10) for next problem
- Time Horizon: Immediate (next problem)
- Priority: Balance challenge with confidence

**Decision Type 3: Problem Type Selection**
- Input: Spacing queue, transfer readiness, curriculum position
- Output: Problem type (new content, retrieval practice, transfer)
- Time Horizon: Near-term (daily)
- Priority: Long-term learning (spacing first)

**Decision Type 4: Scaffolding Level**
- Input: Concept complexity, student capability, prior success
- Output: Scaffolding level (none, minimal, moderate, high)
- Time Horizon: Immediate
- Priority: Support learning without creating dependence

**Decision Type 5: Pacing Adjustment**
- Input: Processing speed estimate, time pressure
- Output: Time allocation multiplier (0.5x to 2.0x baseline)
- Time Horizon: Immediate
- Priority: Equity (process speed ≠ ability)

**Decision Type 6: Conflict Resolution**
- Input: Student preference vs. pedagogical recommendation
- Output: Decision with justification
- Time Horizon: Immediate
- Priority: Wellbeing > Learning > Performance > Coverage

---

## ALGORITHMS

### Algorithm 1: Curriculum Sequencing

```python
def select_next_concept(
    student_id: str,
    domain: str,
    current_concept: str,
    student_profile: StudentOutcomeProfile
) -> Tuple[str, float, str]:
    """
    Determine next concept to teach.
    
    Returns: (recommended_concept, confidence, reasoning)
    
    Logic:
    1. Find concepts with prerequisites met
    2. Among those, find concepts student hasn't mastered
    3. Prioritize by:
       - Foundational importance
       - Student readiness
       - Spacing requirements (when was this concept last practiced?)
    """
    
    # Get curriculum graph (prerequisites)
    prerequisite_graph = get_curriculum_graph(domain)
    
    # Find concepts with all prerequisites satisfied
    candidates = []
    for concept, prereqs in prerequisite_graph.items():
        if all(has_mastered(student_id, prereq) for prereq in prereqs):
            # Student has completed all prerequisites
            mastery = get_mastery_level(student_id, concept)
            if mastery < 0.8:  # Not yet mastered (80%+)
                candidates.append((concept, mastery, get_priority(concept)))
    
    if not candidates:
        return None, 0.0, "No suitable concepts available"
    
    # Score candidates
    best_concept = None
    best_score = -1
    reasoning = ""
    
    for concept, mastery, priority in candidates:
        # Score components
        readiness = 1.0 - mastery  # Not yet mastered
        spacing_need = calculate_spacing_need(student_id, concept)
        importance = priority
        
        # Weighted score: long-term learning prioritized
        score = (0.5 * importance) + (0.3 * readiness) + (0.2 * spacing_need)
        
        if score > best_score:
            best_score = score
            best_concept = concept
            reasoning = f"Priority: {importance:.2f}, Readiness: {readiness:.2f}, Spacing need: {spacing_need:.2f}"
    
    confidence = calculate_curriculum_confidence(student_id, domain, best_concept)
    
    return best_concept, confidence, reasoning
```

### Algorithm 2: Complexity Selection

```python
def select_complexity_level(
    student_id: str,
    concept: str,
    problem_type: str,
    student_profile: StudentOutcomeProfile
) -> Tuple[int, str]:
    """
    Select appropriate complexity level (1-10).
    
    Returns: (complexity_level, reasoning)
    
    Logic:
    - Understanding dimension → can handle higher complexity
    - Misconceptions present → reduce complexity for targeted practice
    - Working memory capacity → don't exceed capacity
    - Recent errors → reduce complexity if overloaded
    """
    
    # Baseline complexity
    baseline = get_baseline_complexity(concept, problem_type)
    
    # Adjustment factors
    adjustments = []
    
    # Factor 1: Understanding level
    understanding = student_profile.outcomes['understanding'].strength
    if understanding > 75:
        adjustment = +2  # Can handle more complex
        adjustments.append(("High understanding", +2))
    elif understanding < 40:
        adjustment = -2  # Need simpler problems
        adjustments.append(("Low understanding", -2))
    
    # Factor 2: Misconceptions present
    if student_profile.misconceptions:
        adjustment = -1.5  # Reduce complexity for targeted remediation
        adjustments.append(("Misconceptions present", -1.5))
    
    # Factor 3: Working memory capacity
    wm_capacity = student_profile.working_memory.estimated_capacity
    max_complexity = wm_capacity * 1.5  # Stretch goal
    baseline = min(baseline, max_complexity)
    
    # Factor 4: Recent cognitive overload
    if check_recent_overload(student_id, concept):
        adjustment = -2  # Significantly reduce if overloaded
        adjustments.append(("Recent overload detected", -2))
    
    # Apply adjustments
    final_complexity = baseline + sum(adj[1] for adj in adjustments)
    final_complexity = max(1, min(10, final_complexity))  # Clamp to [1, 10]
    
    reasoning = "; ".join([f"{name}: {adj:+.1f}" for name, adj in adjustments])
    
    return int(final_complexity), reasoning
```

### Algorithm 3: Conflict Resolution

```python
def resolve_conflict(
    student_id: str,
    student_preference: str,
    pedagogical_recommendation: str,
    student_profile: StudentOutcomeProfile
) -> Tuple[str, str, bool]:
    """
    Resolve conflict between student preference and pedagogy.
    
    Returns: (decision, explanation, override_student)
    
    Priority Order:
    1. Wellbeing (never override if doing so damages wellbeing)
    2. Learning (prioritize long-term learning)
    3. Performance (exam prep is secondary)
    4. Coverage (breadth is lowest priority)
    """
    
    # Check wellbeing constraint first
    if conflicts_with_wellbeing(student_preference, student_profile):
        decision = pedagogical_recommendation
        explanation = "Following pedagogical recommendation for student wellbeing."
        override = True
        return decision, explanation, override
    
    # Check time-to-exam constraint
    days_until_exam = get_days_to_exam(student_id)
    
    if days_until_exam > 180:
        # Exam >6 months away: prioritize long-term learning
        long_term_value_pref = estimate_long_term_value(student_preference)
        long_term_value_rec = estimate_long_term_value(pedagogical_recommendation)
        
        if long_term_value_rec > long_term_value_pref:
            decision = pedagogical_recommendation
            explanation = f"Prioritizing long-term learning. Your choice builds short-term skills; our recommendation builds lasting understanding."
            override = True
        else:
            decision = student_preference
            explanation = "Your preference aligns with long-term learning goals."
            override = False
    
    elif days_until_exam > 42:
        # Exam 6 weeks to 6 months: balance
        balance_score = 0.6 * estimate_long_term_value(pedagogical_recommendation) + \
                       0.4 * estimate_short_term_value(student_preference)
        
        if balance_score > estimate_preference_value(student_preference):
            decision = pedagogical_recommendation
            explanation = "Balancing deep learning with exam prep. Recommend following pedagogical guidance for next 2 weeks."
            override = True
        else:
            decision = student_preference
            override = False
    
    else:
        # Exam <6 weeks: exam prep becomes important but not overriding
        if check_fundamental_gaps(student_id):
            decision = pedagogical_recommendation
            explanation = "Foundational gaps detected. Addressing these now is essential for exam success."
            override = True
        else:
            decision = student_preference
            explanation = "Fundamentals solid. You can proceed with your preference."
            override = False
    
    return decision, explanation, override
```

### Algorithm 4: Cognitive Load Monitoring

```python
def detect_cognitive_overload(
    student_id: str,
    recent_events: List[LearningEvent],
    time_window_minutes: int = 30
) -> Tuple[bool, float, List[str]]:
    """
    Detect if student is experiencing cognitive overload.
    
    Returns: (is_overloaded, severity, indicators)
    
    Indicators:
    - Error rate spike
    - Response time increase
    - Self-reported confusion
    - Rapid guessing
    """
    
    # Aggregate recent performance
    recent_correct = sum(1 for e in recent_events if e.performance['correct'])
    recent_count = len(recent_events)
    recent_error_rate = 1.0 - (recent_correct / recent_count) if recent_count > 0 else 0
    
    # Historical baseline
    historical_error_rate = get_historical_error_rate(student_id, recent_events[0].domain)
    
    indicators = []
    severity_score = 0.0
    
    # Indicator 1: Error rate spike
    error_spike = recent_error_rate - historical_error_rate
    if error_spike > 0.2:  # 20% increase
        indicators.append(f"Error rate spike: {error_spike:.1%}")
        severity_score += 0.4
    
    # Indicator 2: Response time increase
    avg_recent_time = sum(e.performance['response_time_ms'] for e in recent_events) / len(recent_events)
    avg_historical_time = get_historical_avg_response_time(student_id)
    time_increase = (avg_recent_time - avg_historical_time) / avg_historical_time
    if time_increase > 0.3:  # 30% slower
        indicators.append(f"Response time {time_increase:.1%} slower")
        severity_score += 0.3
    
    # Indicator 3: Rapid guessing (short response time + high error rate)
    fast_responses = sum(1 for e in recent_events if e.performance['response_time_ms'] < 2000)
    if fast_responses > recent_count * 0.5 and recent_error_rate > 0.5:
        indicators.append("Rapid guessing pattern detected")
        severity_score += 0.3
    
    is_overloaded = severity_score > 0.5
    
    return is_overloaded, severity_score, indicators
```

### Algorithm 5: Pacing Adjustment

```python
def calculate_pacing_adjustment(
    student_id: str,
    student_profile: StudentOutcomeProfile
) -> Tuple[float, str]:
    """
    Calculate time allocation multiplier based on processing speed.
    
    Returns: (time_multiplier, reasoning)
    
    Time multiplier:
    - 0.5x: Very fast processor (gets more problems in same time)
    - 1.0x: Average (baseline time)
    - 2.0x: Slow processor (needs more time, no penalty)
    """
    
    # Estimate processing speed from response time patterns
    response_times = get_response_time_distribution(student_id)
    
    percentile_25 = numpy.percentile(response_times, 25)
    percentile_75 = numpy.percentile(response_times, 75)
    
    # Students in bottom 25% get less time (they're fast)
    # Students in top 25% get more time (they're slow)
    
    if percentile_75 < 3000:  # Fast: median time <3s
        multiplier = 0.7
        reasoning = "Fast processor: offering additional challenge in same time window"
    elif percentile_75 > 15000:  # Slow: median time >15s
        multiplier = 1.5
        reasoning = "Slower processing speed: extending time allocation"
    else:
        multiplier = 1.0
        reasoning = "Average processing speed"
    
    return multiplier, reasoning
```

---

## DECISION OUTPUT FORMAT

Every PRE decision produces a complete explanation:

```python
PedagogicalDecision {
  decision_id: UUID
  student_id: UUID
  timestamp: DateTime
  
  recommendation: {
    concept: String
    complexity_level: Int  # 1-10
    problem_type: String  # "new", "retrieval", "transfer"
    scaffolding_level: String  # "none", "minimal", "moderate", "high"
    time_allocation_multiplier: Float  # 0.5 to 2.0
  },
  
  reasoning: {
    curriculum_reasoning: String
    complexity_reasoning: String
    pacing_reasoning: String
    scaffolding_reasoning: String
  },
  
  conflict_resolution: {
    student_preference: String (if any)
    recommendation: String
    decision: String
    override_rationale: String (if student preference overridden)
    explanation_to_student: String  # Always explain to student
  },
  
  cognitive_status: {
    is_overloaded: Boolean
    overload_severity: Float (0-1)
    overload_indicators: [String]
  },
  
  confidence: {
    overall_confidence: Float (0-1)
    curriculum_confidence: Float
    complexity_confidence: Float
    reasoning_confidence: Float
  }
}
```

---

## OBSERVABLE BEHAVIORS (Constitutional Requirements)

### Observable Behavior 1: PRE Recommends Spacing Over Student Preference

**How to Verify:**
1. Find a student who prefers new content (has expressed this)
2. When spacing/retrieval practice is scheduled for that student
3. Verify: PRE recommends retrieval practice anyway
4. Verify: Explanation provided to student explaining why

---

### Observable Behavior 2: PRE Adjusts Complexity When Cognitive Overload Detected

**How to Verify:**
1. Select a student showing overload indicators (error spike, slow response)
2. Verify: PRE's next recommendation has reduced complexity
3. Verify: Overload indicators logged in decision record
4. Verify: Student offered simplification before frustration

---

### Observable Behavior 3: PRE Prioritizes Understanding Over Performance

**How to Verify:**
1. Select exams >6 months away
2. Student's preference: "Skip foundational review, move to new topics"
3. PRE's recommendation: "Review foundations first"
4. Verify: Time-to-exam logic prioritizes understanding
5. Verify: Explanation provided to student

---

## INTEGRATION POINTS

### Inputs from Other Systems

```
FROM Student Intelligence System:
  ├─ Student profile (8 dimensions)
  ├─ Working memory capacity
  ├─ Identified misconceptions
  └─ Confidence calibration

FROM Student Context:
  ├─ Current curriculum position
  ├─ Time to exam
  ├─ Student goal/preference
  ├─ Recent problem attempts
  └─ Spacing queue status

FROM Problem Generation System:
  ├─ Available problem types
  └─ Problem difficulty levels
```

### Outputs to Other Systems

```
TO Problem Generation System:
  ├─ Recommended concept
  ├─ Complexity level (1-10)
  ├─ Problem type (new/retrieval/transfer)
  ├─ Scaffolding level
  └─ Time allocation

TO Student (UI):
  ├─ Recommended next problem
  ├─ Explanation of why
  ├─ Conflict resolution (if applicable)
  └─ Time allocation for this problem

TO Progress Tracking:
  ├─ Pedagogical decisions made
  ├─ Reasoning for decisions
  └─ Conflict resolutions
```

---

## AUDIT PROCEDURE

```
AUDIT STEP 1: Decision Logging
  FOR 100 random decisions:
    VERIFY decision has:
      - student_id
      - timestamp
      - recommendation (concept, complexity, type, scaffolding)
      - reasoning (all components)
      - confidence scores
  RESULT: All decisions fully logged ✓

AUDIT STEP 2: Long-Term Learning Priority
  FOR decisions made with exam >6 months away:
    VERIFY complexity selected for learning vs. short-term performance
    CHECK that recommendation aligns with curriculum prerequisites
  RESULT: Long-term learning prioritized ✓

AUDIT STEP 3: Conflict Resolution Transparency
  FOR decisions where student preference was overridden:
    VERIFY explanation provided to student
    VERIFY reasoning documented
    VERIFY student could escalate if disagreement persists
  RESULT: Conflicts resolved transparently ✓

AUDIT STEP 4: Cognitive Load Management
  FOR students showing overload indicators:
    VERIFY PRE recommended complexity reduction
    VERIFY offer of simplification before frustration
  RESULT: Cognitive load managed proactively ✓

AUDIT STEP 5: Pacing Equity
  FOR students with different processing speeds:
    VERIFY faster students get challenge in same time
    VERIFY slower students get extended time without penalty
  RESULT: Pacing equitable ✓
```

---

## IMPLEMENTATION ROADMAP

**Phase 1: Decision Framework (Week 1)**
- Implement curriculum sequencing algorithm
- Create decision tree structure
- Set up logging infrastructure

**Phase 2: Core Algorithms (Week 2)**
- Implement complexity selection
- Add conflict resolution
- Add cognitive load detection

**Phase 3: Pacing & Scaffolding (Week 3)**
- Implement pacing adjuster
- Create scaffolding manager
- Add time allocation logic

**Phase 4: Integration (Week 4)**
- Connect to SIS for student profiles
- Connect to Problem Generation System
- Add UI explanation generation
- Performance optimization

**Phase 5: Testing & Validation (Week 5)**
- Unit tests for all algorithms
- Integration tests with SIS and Problem Gen
- Constitutional compliance audit
- User testing with students

---

## TECHNICAL REQUIREMENTS

**Language:** Python 3.9+  
**Rule Engine:** Drools or custom decision tree implementation  
**Database:** PostgreSQL for decision logging  
**Caching:** Redis for fast lookups  
**Dependencies:** pandas, numpy  
**Testing:** pytest with >85% coverage  

**Performance SLAs:**
- Decision generation: <500ms (p99)
- Conflict resolution: <300ms (p99)
- Cognitive load detection: <200ms (p99)

---

**SPECIFICATION COMPLETE**

PRE is the brain of Adapt. Once complete, Problem Generation System can use PRE's recommendations to create specific problems.

**Next:** SPEC-PGS-001 (Problem Generation System)
