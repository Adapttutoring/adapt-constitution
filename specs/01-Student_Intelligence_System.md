# STUDENT INTELLIGENCE SYSTEM (SIS) — IMPLEMENTATION SPECIFICATION

**Document ID:** SPEC-SIS-001  
**Constitutional Grounding:** EDU-REQ-0101, EDU-REQ-0201, EDU-REQ-0209  
**Version:** 1.0  
**Status:** READY FOR IMPLEMENTATION

---

## EXECUTIVE SUMMARY

The Student Intelligence System (SIS) is the foundational model of each learner across 8 dimensions of educational outcomes. Every pedagogical decision in Adapt depends on SIS estimates. This spec defines the system architecture, data model, estimation algorithms, and observable behaviors required by the Constitution.

**Key Responsibility:** Maintain accurate, multidimensional estimates of each student's learning state, updated in real-time from problem-solving behavior.

---

## CONSTITUTIONAL REQUIREMENTS

| Requirement | Obligation | Impact on SIS |
|-------------|-----------|----------------|
| EDU-REQ-0101 | Model students as developing learners across 8 multidimensional outcomes | SIS tracks all 8 dimensions |
| EDU-REQ-0201 | Estimate working memory capacity from performance patterns | SIS infers working memory from error rates and response times |
| EDU-REQ-0209 | Diagnose misconceptions from error patterns | SIS analyzes errors to identify systematic misconceptions |

**Observable Behaviors (from Constitution):**
- System maintains profiles for each student showing estimated status on 8 outcome dimensions
- Profiles updated after each learning event
- Different students with identical test scores may have different profiles
- System distinguishes guessing from understanding
- System tracks confidence-to-performance correlation
- System records problem type when errors occur

---

## SYSTEM ARCHITECTURE

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  ADAPT PLATFORM                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │  STUDENT INTELLIGENCE SYSTEM (SIS)               │   │
│  │                                                  │   │
│  │  ┌────────────────────────────────────────────┐  │   │
│  │  │ Student Profile Store                      │  │   │
│  │  │ (8-dimensional outcome estimates)          │  │   │
│  │  └────────────────────────────────────────────┘  │   │
│  │           ▲            │            │            │   │
│  │           │            ▼            ▼            │   │
│  │  ┌──────────────┐ ┌────────────┐ ┌──────────┐   │   │
│  │  │ Estimator    │ │ Learning   │ │ Working  │   │   │
│  │  │ (Bayesian)   │ │ Event Log  │ │ Memory   │   │   │
│  │  └──────────────┘ └────────────┘ │ Analyzer │   │   │
│  │                                   └──────────┘   │   │
│  │  ┌────────────────────────────────────────────┐  │   │
│  │  │ Error Analyzer (Misconception Detection)   │  │   │
│  │  └────────────────────────────────────────────┘  │   │
│  └──────────────────────────────────────────────────┘   │
│           ▲                              │              │
│           │                              ▼              │
│  ┌────────────────────────────────────────────────┐   │
│  │ Learning Events (from Problem Solving)         │   │
│  │ ← Problem Generation System                    │   │
│  │ ← Pedagogical Reasoning Engine                 │   │
│  │ ← Explanation System                           │   │
│  │ ← Feedback System                              │   │
│  └────────────────────────────────────────────────┘   │
│           ▲                                            │
│           │                                            │
│  ┌────────────────────────────────────────────────┐   │
│  │ Output: Student Profiles                       │   │
│  │ → Pedagogical Reasoning Engine (decisions)     │   │
│  │ → Problem Generation (next problem)            │   │
│  │ → Progress Reporting (display)                 │   │
│  │ → Assessment System (validation)               │   │
│  └────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Component Breakdown

| Component | Purpose | Technology |
|-----------|---------|-----------|
| **Student Profile Store** | Maintains current outcome estimates per student per concept | PostgreSQL (primary), Redis (cache) |
| **Learning Event Log** | Immutable record of all problem attempts | PostgreSQL (append-only) |
| **Estimator** | Bayesian update of outcome dimensions | Python (Scipy/Numpy) |
| **Working Memory Analyzer** | Infers WM capacity from performance patterns | Python (statistical analysis) |
| **Error Analyzer** | Diagnoses misconceptions from error patterns | Python + NLP for open-ended responses |

---

## DATA MODEL

### Core Entity: Student Outcome Profile

Each student has a profile for each domain (Math, English, Science, etc.).

```python
StudentOutcomeProfile {
  student_id: UUID
  domain: String  # "Math", "English", "Biology", etc.
  
  outcomes: {
    understanding: {
      strength: Float (0-100)  # Depth of conceptual knowledge
      confidence: Float (0-1)   # Model certainty in estimate
      last_updated: DateTime
      evidence_count: Int       # Number of learning events supporting this
    },
    
    retention: {
      strength: Float (0-100)  # Predicted retention after delay
      confidence: Float (0-1)
      last_updated: DateTime
      evidence_count: Int
    },
    
    transfer: {
      near_transfer_strength: Float (0-100)  # Application in similar contexts
      far_transfer_strength: Float (0-100)   # Application in different domains
      confidence: Float (0-1)
      last_updated: DateTime
    },
    
    metacognition: {
      accuracy: Float (-1 to 1)  # Correlation between confidence and performance
                                  # Positive = well-calibrated
                                  # Negative = overconfident
      confidence: Float (0-1)
      last_updated: DateTime
    },
    
    motivation: {
      intrinsic_motivation: Float (0-100)  # Engagement without external rewards
      confidence: Float (0-1)
      last_updated: DateTime
    },
    
    confidence: {
      calibrated_confidence: Float (0-100)  # Confidence matched to ability
      confidence: Float (0-1)
      last_updated: DateTime
    },
    
    independence: {
      learning_independence: Float (0-100)  # Ability to learn without scaffolding
      confidence: Float (0-1)
      last_updated: DateTime
    },
    
    wellbeing: {
      wellbeing_score: Float (0-100)  # Psychological safety, absence of anxiety
      confidence: Float (0-1)
      last_updated: DateTime
    }
  },
  
  # Working Memory Profile
  working_memory: {
    estimated_capacity: Int  # Typical number of chunks student can handle
    confidence: Float (0-1)
    complexity_levels_tested: [Int]  # Complexity levels attempted
    last_updated: DateTime
  },
  
  # Misconceptions Identified
  misconceptions: [
    {
      concept_id: UUID
      misconception_description: String
      error_pattern: String  # What the student systematically gets wrong
      evidence_count: Int  # How many times pattern observed
      first_detected: DateTime
      last_observed: DateTime
      remediation_recommended: Boolean
    }
  ]
}
```

### Core Entity: Learning Event

```python
LearningEvent {
  event_id: UUID
  student_id: UUID
  timestamp: DateTime  # CRITICAL: Precise timing for spacing calculations
  
  problem: {
    problem_id: UUID
    domain: String  # "Math", "English", etc.
    concept: String  # "Quadratic Equations", "Shakespearean Sonnets"
    complexity_level: Int  # 1 (novice) to 10 (expert)
    problem_type: String  # "procedural", "conceptual", "transfer"
    
    context: String  # "blocked" (same topic) vs "interleaved" (mixed topics)
    is_transfer_problem: Boolean
    transfer_distance: String  # "near" or "far"
  },
  
  performance: {
    response_provided: String  # Student's answer
    correct: Boolean
    success_rate_on_this_problem: Float  # If student attempted before
    response_time_ms: Int
    
    # For multiple-choice or structured responses
    confidence_rating: Float (0-1)  # Student's self-reported confidence
    worked_with_hints: Boolean  # Did student use hints?
  },
  
  # Error Analysis (only if incorrect)
  error_analysis: {
    error_type: String  # "careless", "misconception", "incomplete_understanding"
    error_category: String  # "computational", "conceptual", "application"
    likely_misconception: String  # If detected
    confidence_in_diagnosis: Float (0-1)
  }
}
```

### Database Schema

```sql
-- Student Profiles
CREATE TABLE student_profiles (
  student_id UUID PRIMARY KEY,
  domain VARCHAR(50) NOT NULL,
  
  -- 8 Outcome Dimensions
  understanding_strength FLOAT,
  understanding_confidence FLOAT,
  understanding_last_updated TIMESTAMP,
  
  retention_strength FLOAT,
  retention_confidence FLOAT,
  retention_last_updated TIMESTAMP,
  
  near_transfer_strength FLOAT,
  far_transfer_strength FLOAT,
  transfer_confidence FLOAT,
  transfer_last_updated TIMESTAMP,
  
  metacognition_accuracy FLOAT,
  metacognition_confidence FLOAT,
  metacognition_last_updated TIMESTAMP,
  
  motivation_strength FLOAT,
  motivation_confidence FLOAT,
  motivation_last_updated TIMESTAMP,
  
  calibrated_confidence_strength FLOAT,
  calibrated_confidence_confidence FLOAT,
  calibrated_confidence_last_updated TIMESTAMP,
  
  independence_strength FLOAT,
  independence_confidence FLOAT,
  independence_last_updated TIMESTAMP,
  
  wellbeing_strength FLOAT,
  wellbeing_confidence FLOAT,
  wellbeing_last_updated TIMESTAMP,
  
  -- Working Memory
  working_memory_capacity INT,
  working_memory_confidence FLOAT,
  working_memory_last_updated TIMESTAMP,
  
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  
  PRIMARY KEY (student_id, domain)
);

-- Immutable Learning Event Log
CREATE TABLE learning_events (
  event_id UUID PRIMARY KEY,
  student_id UUID NOT NULL,
  timestamp TIMESTAMP NOT NULL,  -- CRITICAL: Precise for spacing
  
  domain VARCHAR(50) NOT NULL,
  concept VARCHAR(200) NOT NULL,
  problem_id UUID NOT NULL,
  complexity_level INT NOT NULL,
  problem_type VARCHAR(50) NOT NULL,
  
  context VARCHAR(50),  -- "blocked" or "interleaved"
  is_transfer_problem BOOLEAN,
  transfer_distance VARCHAR(20),
  
  response_text TEXT,
  correct BOOLEAN NOT NULL,
  response_time_ms INT,
  confidence_rating FLOAT,
  worked_with_hints BOOLEAN,
  
  error_type VARCHAR(50),
  error_category VARCHAR(50),
  likely_misconception TEXT,
  misconception_confidence FLOAT,
  
  created_at TIMESTAMP DEFAULT NOW(),
  CONSTRAINT immutable_events CHECK (created_at = CURRENT_TIMESTAMP)
);

-- Misconceptions Tracking
CREATE TABLE misconceptions (
  misconception_id UUID PRIMARY KEY,
  student_id UUID NOT NULL,
  concept VARCHAR(200) NOT NULL,
  misconception_description TEXT NOT NULL,
  error_pattern TEXT NOT NULL,
  evidence_count INT DEFAULT 1,
  first_detected TIMESTAMP DEFAULT NOW(),
  last_observed TIMESTAMP DEFAULT NOW(),
  remediation_recommended BOOLEAN DEFAULT FALSE,
  
  FOREIGN KEY (student_id) REFERENCES students(id)
);

-- Index for performance
CREATE INDEX idx_learning_events_student_timestamp 
  ON learning_events(student_id, timestamp);
CREATE INDEX idx_learning_events_concept 
  ON learning_events(student_id, domain, concept);
```

---

## ALGORITHMS

### Algorithm 1: Bayesian Outcome Estimator

Estimates each outcome dimension using Bayesian inference.

```python
def estimate_outcome_dimension(
    student_id: str,
    domain: str,
    concept: str,
    outcome_type: str,  # "understanding", "retention", etc.
    new_learning_event: LearningEvent
) -> Tuple[float, float]:
    """
    Update estimate for one outcome dimension.
    
    Returns: (new_strength, confidence)
    """
    
    # Get prior estimate
    prior_strength = get_prior_strength(student_id, domain, outcome_type)
    prior_confidence = get_prior_confidence(student_id, domain, outcome_type)
    
    # Calculate likelihood based on problem result
    likelihood = calculate_likelihood(
        new_learning_event.correct,
        new_learning_event.complexity_level,
        new_learning_event.response_time_ms,
        outcome_type
    )
    
    # Bayesian update
    posterior_strength = bayesian_update(
        prior_strength,
        likelihood,
        prior_confidence
    )
    
    # Update confidence based on evidence count
    evidence_count = get_evidence_count(student_id, domain, concept, outcome_type)
    posterior_confidence = min(
        prior_confidence + (0.02 * evidence_count),  # Confidence increases with evidence
        0.95  # Cap at 95%
    )
    
    return posterior_strength, posterior_confidence


def bayesian_update(prior: float, likelihood: float, confidence: float) -> float:
    """
    Bayesian update formula:
    posterior = (likelihood * prior + (1 - likelihood) * 0.5) * confidence + prior * (1 - confidence)
    
    Interpretation:
    - If likelihood is high (student succeeded), posterior moves toward 1.0
    - If likelihood is low (student failed), posterior moves toward 0.0
    - Confidence weights how much the new evidence matters
    """
    
    if confidence == 0:
        return prior  # No confidence, don't update
    
    likelihood_weighted = likelihood * prior + (1 - likelihood) * 0.5
    posterior = likelihood_weighted * confidence + prior * (1 - confidence)
    
    return max(0.0, min(1.0, posterior))  # Clamp to [0, 1]


def calculate_likelihood(
    correct: bool,
    complexity_level: int,
    response_time_ms: int,
    outcome_type: str
) -> float:
    """
    Calculate likelihood of outcome dimension based on performance signals.
    
    For Understanding:
      - Correct on complex problem → high likelihood of understanding
      - Correct on simple problem → lower likelihood (could be luck)
      - Correct with short response time → higher understanding
    
    For Retention:
      - Performance on delayed retrieval problems
      - Retention problems scheduled after delay
    
    For Transfer:
      - Performance on transfer (novel context) problems
      - Near transfer vs. far transfer performance
    """
    
    base_likelihood = 0.7 if correct else 0.3
    
    # Complexity adjustment
    complexity_factor = (complexity_level / 10.0)  # 0.0 to 1.0
    if correct:
        base_likelihood += 0.15 * complexity_factor
    else:
        base_likelihood -= 0.1 * complexity_factor
    
    # Response time adjustment (fast = more confident in understanding)
    if response_time_ms < 5000:  # Fast response
        base_likelihood += 0.1
    elif response_time_ms > 30000:  # Very slow
        base_likelihood -= 0.1
    
    return max(0.0, min(1.0, base_likelihood))
```

### Algorithm 2: Working Memory Capacity Estimator

```python
def estimate_working_memory_capacity(student_id: str, domain: str) -> Tuple[int, float]:
    """
    Estimate student's working memory capacity from error patterns across complexity levels.
    
    Working memory is typically ~4 chunks, but varies by:
    - Familiarity (familiar domain = larger effective chunks)
    - Cognitive load management
    
    Returns: (estimated_capacity, confidence)
    """
    
    # Get performance across complexity levels
    performance_by_complexity = get_performance_by_complexity(
        student_id, domain
    )
    
    # Find "break point" where error rate increases significantly
    error_rates = [
        (level, error_rate)
        for level, error_rate in performance_by_complexity
    ]
    
    # Find the complexity level where errors start increasing
    break_point = find_error_breakpoint(error_rates)
    
    # Map complexity level to working memory chunks
    # Complexity 1-3: ~2 chunks
    # Complexity 4-6: ~4 chunks
    # Complexity 7-9: ~6+ chunks
    estimated_capacity = (break_point / 2)  # Rough mapping
    
    # Confidence based on number of data points
    num_attempts = sum(count for _, _, count in get_attempts_by_complexity(student_id, domain))
    confidence = min(0.95, 0.5 + (num_attempts / 50))  # 50 attempts → 95% confidence
    
    return int(estimated_capacity), confidence


def find_error_breakpoint(error_rates: List[Tuple[int, float]]) -> int:
    """
    Find complexity level where error rate increases significantly.
    
    Example:
    Level 1: 5% errors
    Level 2: 8% errors
    Level 3: 12% errors
    Level 4: 35% errors  ← BREAKPOINT (large jump)
    Level 5: 55% errors
    
    Returns the level just before the breakpoint.
    """
    
    if len(error_rates) < 2:
        return 5  # Default assumption
    
    max_increase = 0
    breakpoint = error_rates[0][0]
    
    for i in range(1, len(error_rates)):
        level_i, error_rate_i = error_rates[i]
        level_prev, error_rate_prev = error_rates[i - 1]
        
        increase = error_rate_i - error_rate_prev
        
        if increase > max_increase and increase > 0.15:  # 15% jump threshold
            max_increase = increase
            breakpoint = level_prev
    
    return breakpoint
```

### Algorithm 3: Misconception Diagnosis

```python
def diagnose_misconception(
    student_id: str,
    domain: str,
    concept: str,
    recent_errors: List[LearningEvent]
) -> Optional[Dict]:
    """
    Identify systematic misconceptions from error patterns.
    
    A misconception is:
    - Same type of error across multiple problem instances
    - Indicates coherent but incorrect mental model
    - Not just careless mistakes
    """
    
    if len(recent_errors) < 3:
        return None  # Need multiple instances to diagnose misconception
    
    # Group errors by type
    error_patterns = {}
    for event in recent_errors:
        if event.error_analysis.error_type == "misconception":
            pattern_key = event.error_analysis.likely_misconception
            error_patterns[pattern_key] = error_patterns.get(pattern_key, 0) + 1
    
    # Find pattern that appears multiple times
    systematic_patterns = {
        pattern: count for pattern, count in error_patterns.items()
        if count >= 2  # At least 2 instances
    }
    
    if not systematic_patterns:
        return None
    
    # Most frequent pattern
    misconception = max(systematic_patterns.items(), key=lambda x: x[1])
    
    return {
        "misconception": misconception[0],
        "instances": misconception[1],
        "confidence": min(0.95, 0.5 + (misconception[1] * 0.2)),
        "remediation_needed": True
    }
```

---

## OBSERVABLE BEHAVIORS (Constitutional Requirements)

### Observable Behavior 1: Student Profiles Maintain 8 Dimensions

**How to Verify:**
1. Query student profile for any student with >10 learning events
2. Verify all 8 outcome dimensions present:
   - ✓ Understanding depth
   - ✓ Long-term retention
   - ✓ Transfer (near and far)
   - ✓ Metacognitive accuracy
   - ✓ Intrinsic motivation
   - ✓ Calibrated confidence
   - ✓ Learning independence
   - ✓ Wellbeing

3. Each dimension has:
   - ✓ Numerical strength estimate (0-100)
   - ✓ Confidence level (0-1)
   - ✓ Last update timestamp

---

### Observable Behavior 2: Different Students with Same Score Have Different Profiles

**How to Verify:**
1. Find 2 students with identical test scores (e.g., both got 80%)
2. Compare their profiles on retention, transfer, and motivation
3. Expected: Profiles differ
   - Student A: High understanding (80%), low retention (40%), medium transfer (60%)
   - Student B: Medium understanding (70%), high retention (85%), high transfer (75%)

---

### Observable Behavior 3: Profiles Update After Each Learning Event

**How to Verify:**
1. Select a student with an existing profile
2. Record profile state at T0
3. Student completes one problem (learning event)
4. Check profile at T1
5. Expected: At least one dimension updated

---

### Observable Behavior 4: Working Memory Estimates Made from Performance Patterns

**How to Verify:**
1. Query working_memory_capacity for any student
2. Verify estimate exists (non-null)
3. Verify it's based on actual performance data:
   - Get attempted problems at different complexity levels
   - Verify capacity correlates with error rates at those levels

---

## INTEGRATION POINTS

### Inputs (Events from Other Systems)

```
FROM Problem Solving System:
  ├─ Student submits response → Learning Event created
  ├─ Timestamp recorded (precise)
  ├─ Correctness evaluated
  └─ Response time captured

FROM Error Analysis System:
  ├─ Error categorized (careless vs. misconception)
  └─ Error pattern identified

FROM Spacing System:
  ├─ Retention test at scheduled interval
  └─ Performance on retrieval practice
```

### Outputs (Used by Other Systems)

```
TO Pedagogical Reasoning Engine:
  ├─ Student profile (all 8 dimensions)
  ├─ Working memory estimate
  ├─ Identified misconceptions
  └─ Confidence calibration

TO Problem Generation System:
  ├─ Next concept readiness
  ├─ Appropriate complexity level
  └─ Misconceptions to target

TO Spacing System:
  ├─ Current retention estimate
  └─ Retention curve

TO Progress Reporting:
  ├─ Learning trajectory
  ├─ Multidimensional progress
  └─ Predicted exam readiness
```

---

## AUDIT PROCEDURE

To verify SIS is functioning correctly:

```
AUDIT STEP 1: Profile Completeness
  FOR each student with >10 learning events:
    VERIFY all 8 outcome dimensions exist
    VERIFY each dimension has (strength, confidence, timestamp)
  RESULT: All profiles complete ✓

AUDIT STEP 2: Update Frequency
  FOR 10 random students:
    RECORD profile at T0
    SIMULATE 5 new learning events
    RECORD profile at T1
    VERIFY at least one dimension changed
  RESULT: Profiles update correctly ✓

AUDIT STEP 3: Working Memory Tracking
  FOR 10 students with data across complexity levels:
    GET working memory estimate
    VERIFY estimate correlates with performance across levels
    VERIFY confidence is based on evidence count
  RESULT: Working memory estimates valid ✓

AUDIT STEP 4: Error Analysis
  FOR 20 random problem attempts marked incorrect:
    VERIFY error_type classified
    FOR errors marked "misconception":
      VERIFY error appears in >2 instances for same student
  RESULT: Error analysis valid ✓

AUDIT STEP 5: Learning Event Immutability
  FOR random learning events:
    ATTEMPT to modify event record
    VERIFY modification rejected
    VERIFY immutability constraint enforced
  RESULT: Events immutable ✓
```

---

## IMPLEMENTATION ROADMAP

**Phase 1: Data Layer (Week 1)**
- Create PostgreSQL schema
- Implement learning event log
- Set up student_profiles table
- Add indexes for performance

**Phase 2: Core Estimator (Week 2)**
- Implement Bayesian outcome estimator
- Create update pipeline
- Add caching layer (Redis)

**Phase 3: Working Memory & Errors (Week 3)**
- Implement working memory capacity estimator
- Add misconception diagnosis
- Create error analysis pipeline

**Phase 4: Integration (Week 4)**
- Connect to Problem Generation System
- Connect to Pedagogical Reasoning Engine
- Add monitoring and logging
- Performance optimization

**Phase 5: Testing (Week 5)**
- Unit tests for all algorithms
- Integration tests with other systems
- Load testing (1000 students, 10k events/day)
- Constitutional compliance audit

---

## TECHNICAL REQUIREMENTS

**Language:** Python 3.9+  
**Database:** PostgreSQL 13+ with TimescaleDB extension (for time-series data)  
**Cache:** Redis 6.0+ for profile caching  
**Dependencies:** scipy, numpy, pandas  
**Testing:** pytest with >90% coverage  

**Performance SLAs:**
- Profile query: <100ms (p99)
- Event ingestion: <50ms (p99)
- Bayesian update: <200ms (p99)
- Working memory estimate: <500ms (p99)

---

**SPECIFICATION COMPLETE**

This spec is ready for engineering implementation. The SIS is the foundation. Once complete, Pedagogical Reasoning Engine and Problem Generation System can proceed.

**Next:** SPEC-PRE-001 (Pedagogical Reasoning Engine)
