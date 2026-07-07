# PROBLEM GENERATION SYSTEM (PGS) — IMPLEMENTATION SPECIFICATION

**Document ID:** SPEC-PGS-001  
**Constitutional Grounding:** EDU-REQ-0205, EDU-REQ-0207, EDU-REQ-0208, EDU-REQ-0212  
**Version:** 1.0  
**Status:** READY FOR IMPLEMENTATION

---

## EXECUTIVE SUMMARY

The Problem Generation System creates problems dynamically based on Pedagogical Reasoning Engine recommendations. Rather than serving static problems from a content library, PGS generates contextually appropriate problems that:

- Implement spacing and retrieval practice schedules
- Include appropriate interleaving ratios
- Challenge without overwhelming
- Include transfer problems for application
- Manage cognitive load
- Are calibrated to student understanding level

This is where the Constitution's learning science principles become concrete problems students solve.

---

## CONSTITUTIONAL REQUIREMENTS

| Requirement | Obligation | PGS Impact |
|---|---|---|
| EDU-REQ-0205 | Minimize extraneous cognitive load | Clear language, organized presentation |
| EDU-REQ-0207 | Retrieval practice in all sequences | Interleave retrieval with new content |
| EDU-REQ-0208 | Interleaving in all practice activities | 30%+ non-primary topics per set |
| EDU-REQ-0212 | Transfer problems in all problem sets | Include near and far transfer problems |

**Observable Behaviors (from Constitution):**
- Problem sequences include retrieval problems
- Problems from multiple topics are interleaved
- At least 30% of problems from non-primary topics
- Students understand why interleaving is hard
- Transfer problems deliberately included
- Transfer difficulty is contextualized (difficulty = learning, not failure)

---

## SYSTEM ARCHITECTURE

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│      PROBLEM GENERATION SYSTEM (PGS)                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  INPUT: Pedagogical Decision from PRE                   │
│  ├─ Concept to teach                                    │
│  ├─ Complexity level (1-10)                             │
│  ├─ Problem type (new/retrieval/transfer)               │
│  ├─ Scaffolding level                                   │
│  └─ Time allocation                                     │
│                                                         │
│  INPUT: Student Profile from SIS                        │
│  ├─ Understanding level                                 │
│  ├─ Misconceptions                                      │
│  └─ Prior attempts at this concept                      │
│                                                         │
│  INPUT: Spacing Queue from Spacing System               │
│  ├─ Concepts due for retrieval                          │
│  └─ Interleaving requirements                           │
│                                                         │
│         ┌────────────────────────────────────┐           │
│         │  SEQUENCE BUILDER                  │           │
│         │  (selects problem types)            │           │
│         │  - Primary problem                  │           │
│         │  - Retrieval problems (spacing)     │           │
│         │  - Interleaved problems             │           │
│         │  - Transfer problems                │           │
│         └────────────────────────────────────┘           │
│                    │                                    │
│                    ▼                                    │
│         ┌────────────────────────────────────┐           │
│         │  PROBLEM GENERATOR                 │           │
│         │  (creates or selects problems)      │           │
│         │  - Template selection               │           │
│         │  - Parameter generation             │           │
│         │  - Distractor generation            │           │
│         └────────────────────────────────────┘           │
│                    │                                    │
│                    ▼                                    │
│         ┌────────────────────────────────────┐           │
│         │  COGNITIVE LOAD OPTIMIZER          │           │
│         │  (reduces unnecessary complexity)   │           │
│         │  - Clear language verification      │           │
│         │  - Irrelevant detail removal        │           │
│         │  - Diagram-to-text balance          │           │
│         │  - Formatting optimization          │           │
│         └────────────────────────────────────┘           │
│                    │                                    │
│                    ▼                                    │
│  OUTPUT: Problem Sequence                               │
│  ├─ Ordered list of problems                            │
│  ├─ Explanations & scaffolds                            │
│  ├─ Student-facing context                              │
│  └─ Metadata (difficulty, type, transfer distance)      │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
    Student Problem Sequence
    (displayed in UI)
```

### Component Breakdown

| Component | Purpose | Input |
|-----------|---------|-------|
| **Sequence Builder** | Determines which problems to include in a sequence | PRE decision + spacing queue |
| **Problem Generator** | Creates/selects specific problems | Concept + complexity + problem type |
| **Cognitive Load Optimizer** | Ensures problems minimize extraneous load | Generated problem |
| **Presentation Manager** | Formats problems for delivery | Optimized problem |

---

## DATA MODEL

### Core Entity: Problem Template

```python
ProblemTemplate {
  template_id: UUID
  domain: String  # "Math", "English", etc.
  topic: String  # "Quadratic Equations", "Shakespeare"
  subtopic: String  # Specific skill
  
  difficulty_range: (Int, Int)  # Min and max complexity
  problem_type: String  # "procedural", "conceptual", "transfer"
  
  template_text: String  # Template with parameterized placeholders
  # Example: "Solve the quadratic equation {{a}}x² + {{b}}x + {{c}} = 0"
  
  parameters: [
    {
      name: String  # "a", "b", "c"
      type: String  # "integer", "real", "concept"
      range: (Float, Float)  # Min and max values
      constraints: String  # "a != 0", etc.
    }
  ],
  
  solution_procedure: [
    {
      step: Int
      description: String
      example_value: String  # Example with concrete values
    }
  ],
  
  scaffolding_options: [
    {
      level: String  # "none", "minimal", "moderate", "high"
      hint_text: String
      worked_example: String  # Worked example for this level
      step_by_step_guide: String  # Detailed steps
    }
  ],
  
  common_misconceptions: [
    {
      misconception: String
      why_students_believe_it: String
      how_to_recognize: String  # What error indicates this
      remediation: String
    }
  ],
  
  transfer_context: [
    {
      context: String  # Real-world or different-domain application
      distance: String  # "near" or "far"
      explanation: String
    }
  ]
}
```

### Core Entity: Generated Problem

```python
GeneratedProblem {
  problem_id: UUID
  student_id: UUID
  template_id: UUID
  
  problem_text: String  # Template filled with parameters
  complexity_level: Int  # 1-10
  problem_type: String  # "new", "retrieval", "transfer"
  
  parameters: Dict  # {"a": 3, "b": -5, "c": 2, ...}
  
  scaffolding: {
    level: String  # What level was selected for this student
    hint: String (optional)
    worked_example: String (optional)
    step_by_step: String (optional)
  },
  
  metadata: {
    is_transfer_problem: Boolean
    transfer_distance: String  # "near" or "far"
    spacing_rationale: String  # Why this problem was selected
    interleaving_context: String  # How it fits in the sequence
    
    # Cognitive load indicators
    estimated_extraneous_load: Float (0-1)
    estimated_intrinsic_load: Float (0-1)  # Inherent difficulty
    
    # For reference
    expected_response_time_ms: Int
    expected_success_rate: Float (0-1)  # Based on student profile
  }
}
```

### Database Schema

```sql
CREATE TABLE problem_templates (
  template_id UUID PRIMARY KEY,
  domain VARCHAR(50) NOT NULL,
  topic VARCHAR(200) NOT NULL,
  subtopic VARCHAR(200) NOT NULL,
  
  problem_type VARCHAR(50) NOT NULL,  -- "procedural", "conceptual", "transfer"
  min_complexity INT NOT NULL,
  max_complexity INT NOT NULL,
  
  template_text TEXT NOT NULL,
  parameters JSON NOT NULL,  -- Parameter definitions
  solution_procedure JSON NOT NULL,
  
  scaffolding_options JSON NOT NULL,
  common_misconceptions JSON NOT NULL,
  transfer_context JSON NOT NULL,
  
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE generated_problems (
  problem_id UUID PRIMARY KEY,
  student_id UUID NOT NULL,
  template_id UUID NOT NULL REFERENCES problem_templates(template_id),
  
  problem_text TEXT NOT NULL,
  complexity_level INT NOT NULL,
  problem_type VARCHAR(50) NOT NULL,
  parameters JSON NOT NULL,
  
  scaffolding_level VARCHAR(50),
  hint TEXT,
  worked_example TEXT,
  step_by_step_guide TEXT,
  
  is_transfer_problem BOOLEAN,
  transfer_distance VARCHAR(20),
  spacing_rationale TEXT,
  interleaving_context TEXT,
  
  extraneous_load FLOAT,
  intrinsic_load FLOAT,
  
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY (student_id) REFERENCES students(id)
);

CREATE INDEX idx_templates_by_domain_topic 
  ON problem_templates(domain, topic, subtopic);
CREATE INDEX idx_generated_problems_student 
  ON generated_problems(student_id, created_at);
```

---

## ALGORITHMS

### Algorithm 1: Problem Sequence Builder

```python
def build_problem_sequence(
    student_id: str,
    pre_decision: PedagogicalDecision,
    spacing_queue: List[str],  # Concepts due for retrieval
    sequence_length: int = 5  # Problems to generate
) -> List[GeneratedProblem]:
    """
    Build a sequence of problems implementing:
    - Primary concept (from PRE decision)
    - Spacing/retrieval practice
    - Interleaving
    - Transfer
    
    Returns: Ordered list of problems
    """
    
    sequence = []
    
    # Problem 1: Primary problem (new or advancing concept)
    primary_problem = generate_problem(
        student_id,
        concept=pre_decision.recommendation.concept,
        complexity=pre_decision.recommendation.complexity_level,
        problem_type=pre_decision.recommendation.problem_type,
        scaffolding=pre_decision.recommendation.scaffolding_level
    )
    sequence.append(primary_problem)
    
    # Problems 2-4: Mix of retrieval (spacing) and interleaving
    retrieval_count = math.ceil(sequence_length * 0.4)  # 40% retrieval
    interleaving_count = math.ceil(sequence_length * 0.3)  # 30% interleaving
    transfer_count = math.floor(sequence_length * 0.2)  # 20% transfer
    
    # Add retrieval practice (spacing queue)
    for i in range(retrieval_count):
        if i < len(spacing_queue):
            concept = spacing_queue[i]
            problem = generate_retrieval_problem(student_id, concept)
            sequence.append(problem)
    
    # Add interleaving problems (different topics)
    for i in range(interleaving_count):
        concept = select_interleaving_concept(
            student_id,
            primary_concept=pre_decision.recommendation.concept
        )
        problem = generate_problem(
            student_id,
            concept=concept,
            problem_type="review",
            complexity=pre_decision.recommendation.complexity_level - 1
        )
        sequence.append(problem)
    
    # Add transfer problems
    for i in range(transfer_count):
        concept = pre_decision.recommendation.concept
        transfer_distance = "near" if i == 0 else "far"
        problem = generate_transfer_problem(
            student_id,
            concept=concept,
            transfer_distance=transfer_distance
        )
        sequence.append(problem)
    
    # Trim to sequence length
    sequence = sequence[:sequence_length]
    
    # Interleave order: don't do all retrieval, then all new
    sequence = randomize_sequence_order(sequence, protect_primary=True)
    
    return sequence
```

### Algorithm 2: Problem Generator

```python
def generate_problem(
    student_id: str,
    concept: str,
    complexity: int,  # 1-10
    problem_type: str,  # "new", "retrieval", "transfer"
    scaffolding: str = "minimal"  # "none", "minimal", "moderate", "high"
) -> GeneratedProblem:
    """
    Generate a specific problem from a template.
    """
    
    # Select appropriate template
    template = select_template(concept, complexity, problem_type)
    
    # Generate parameters
    parameters = generate_parameters(
        template,
        complexity,
        avoid_previous_values=True  # Don't repeat exact same numbers
    )
    
    # Fill template
    problem_text = fill_template(template.template_text, parameters)
    
    # Generate distractor options (if multiple choice)
    if template.has_options:
        correct_answer = calculate_answer(template, parameters)
        distractors = generate_distractors(
            correct_answer,
            template,
            student_misconceptions=get_student_misconceptions(student_id, concept)
        )
        problem_text += "\nOptions: " + format_options([correct_answer] + distractors)
    
    # Get scaffolding if needed
    scaffolding_text = None
    if scaffolding != "none":
        scaffolding_text = generate_scaffolding(
            template,
            parameters,
            level=scaffolding
        )
    
    # Create problem object
    problem = GeneratedProblem(
        problem_id=generate_uuid(),
        student_id=student_id,
        template_id=template.template_id,
        problem_text=problem_text,
        complexity_level=complexity,
        problem_type=problem_type,
        parameters=parameters,
        scaffolding=scaffolding_text
    )
    
    return problem
```

### Algorithm 3: Cognitive Load Optimizer

```python
def optimize_cognitive_load(problem: GeneratedProblem) -> GeneratedProblem:
    """
    Reduce extraneous cognitive load (while preserving intrinsic load).
    
    Checks:
    1. Language clarity
    2. Irrelevant detail removal
    3. Diagram-to-text balance
    4. Formatting alignment
    """
    
    # Step 1: Clarity check
    clarity_issues = check_language_clarity(problem.problem_text)
    for issue in clarity_issues:
        problem.problem_text = simplify_language(problem.problem_text, issue)
    
    # Step 2: Remove irrelevant details
    relevant_terms = extract_relevant_terms(problem)
    problem.problem_text = remove_irrelevant_details(
        problem.problem_text,
        relevant_terms
    )
    
    # Step 3: Optimize formatting
    problem.problem_text = format_for_clarity(problem.problem_text)
    
    # Step 4: Estimate cognitive loads
    extraneous = estimate_extraneous_load(problem.problem_text)
    intrinsic = estimate_intrinsic_load(
        problem.problem_type,
        problem.complexity_level
    )
    
    if extraneous + intrinsic > 0.9:  # Overloaded
        # Try simplified version
        simplified = simplify_problem(problem)
        if estimate_extraneous_load(simplified) < extraneous:
            problem = simplified
    
    problem.metadata.estimated_extraneous_load = extraneous
    problem.metadata.estimated_intrinsic_load = intrinsic
    
    return problem


def estimate_extraneous_load(problem_text: str) -> float:
    """
    Estimate unnecessary cognitive load from problem presentation.
    
    Returns: 0.0 (minimal) to 1.0 (excessive)
    """
    
    score = 0.0
    
    # Word count (long = harder to parse)
    word_count = len(problem_text.split())
    if word_count > 200:
        score += 0.3
    elif word_count > 100:
        score += 0.1
    
    # Sentence complexity
    sentences = problem_text.split('.')
    for sentence in sentences:
        if len(sentence.split()) > 30:  # Long sentence
            score += 0.1
    
    # Jargon density
    jargon_count = count_unnecessary_jargon(problem_text)
    score += min(0.2, jargon_count * 0.05)
    
    # Formatting issues
    if not is_well_formatted(problem_text):
        score += 0.2
    
    return min(1.0, score)
```

### Algorithm 4: Interleaving Selector

```python
def select_interleaving_concept(
    student_id: str,
    primary_concept: str,
    avoid_recent: int = 3  # Avoid concepts from last 3 problems
) -> str:
    """
    Select a concept for interleaving that:
    - Is different from primary concept
    - Student is learning (not mastered, not completely unfamiliar)
    - Hasn't appeared in recent problems
    - Is from related but different topic
    """
    
    # Get student's current learning topics
    topics_in_progress = get_topics_student_is_learning(student_id)
    
    # Remove primary and recent
    recent_concepts = get_recent_problems(student_id, avoid_recent)
    candidates = [
        t for t in topics_in_progress
        if t != primary_concept and t not in recent_concepts
    ]
    
    if not candidates:
        # Fallback: any topic not mastered
        candidates = [
            t for t in topics_in_progress
            if get_mastery(student_id, t) < 0.85
        ]
    
    if not candidates:
        return primary_concept  # Fallback
    
    # Weight by mastery level (pick topics at intermediate level)
    weights = []
    for concept in candidates:
        mastery = get_mastery(student_id, concept)
        # Weight: highest for 40-70% mastery (learning zone)
        weight = 1.0 if 0.4 < mastery < 0.7 else 0.3
        weights.append((concept, weight))
    
    selected = weighted_random_choice(weights)
    return selected
```

---

## OBSERVABLE BEHAVIORS (Constitutional Requirements)

### Observable Behavior 1: Retrieval Practice in All Sequences

**How to Verify:**
1. Generate 20 problem sequences for different students
2. For each sequence:
   - Count retrieval problems (previously learned content)
   - Verify retrieval is interleaved with new content
   - Verify retrieval ≥30% of sequence
3. Expected: 100% of sequences include retrieval

---

### Observable Behavior 2: 30%+ Interleaving

**How to Verify:**
1. Generate 50 problem sequences
2. For each sequence:
   - Categorize by topic
   - Count problems from non-primary topics
   - Calculate interleaving ratio
3. Expected: 100% of sequences have ≥30% non-primary problems

---

### Observable Behavior 3: Transfer Problems Included

**How to Verify:**
1. Generate 30 problem sequences
2. For each sequence:
   - Count transfer problems (novel context applications)
   - Count near transfer vs. far transfer
3. Expected: 100% of sequences include transfer problems

---

### Observable Behavior 4: Cognitive Load Minimized

**How to Verify:**
1. Generate 20 problems at various complexity levels
2. For each problem:
   - Rate language clarity (1-5)
   - Count irrelevant details
   - Check formatting alignment
3. Expected: Average clarity ≥4.0, minimal irrelevant detail

---

## INTEGRATION POINTS

### Inputs from Other Systems

```
FROM Pedagogical Reasoning Engine:
  ├─ Recommended concept
  ├─ Complexity level
  ├─ Problem type
  ├─ Scaffolding level
  └─ Time allocation

FROM Spacing System:
  ├─ Concepts due for retrieval
  └─ Spacing schedule

FROM Student Intelligence System:
  ├─ Student misconceptions
  ├─ Understanding level
  └─ Prior attempts

FROM Content Repository:
  ├─ Problem templates
  └─ Template parameters
```

### Outputs to Other Systems

```
TO Student:
  ├─ Problem sequence (5-10 problems)
  ├─ Scaffolding & hints (if applicable)
  └─ Explanation of why this sequence

TO Learning Event Log:
  ├─ Generated problem metadata
  ├─ Complexity assigned
  ├─ Problem type (new/retrieval/transfer)
  └─ Spacing rationale

TO Pedagogical Reasoning Engine (feedback):
  ├─ Student performance on sequence
  └─ Time spent
```

---

## AUDIT PROCEDURE

```
AUDIT STEP 1: Retrieval Practice
  FOR 20 random sequences:
    COUNT retrieval problems
    VERIFY retrieval ≥30% of sequence
    VERIFY retrieval interleaved (not all at end)
  RESULT: Retrieval practice present and interleaved ✓

AUDIT STEP 2: Interleaving Ratio
  FOR 30 random sequences:
    CATEGORIZE problems by topic
    COUNT non-primary topic problems
    VERIFY interleaving ratio ≥30%
  RESULT: Adequate interleaving ✓

AUDIT STEP 3: Transfer Problems
  FOR 20 random sequences:
    IDENTIFY transfer problems
    VERIFY both near and far transfer included
    VERIFY transfer difficulty contextualized for student
  RESULT: Transfer practice deliberate ✓

AUDIT STEP 4: Cognitive Load
  FOR 30 random problems:
    EVALUATE language clarity
    COUNT irrelevant details
    CHECK formatting alignment
    MEASURE estimated extraneous load
  RESULT: Cognitive load minimized ✓

AUDIT STEP 5: Scaffolding Consistency
  FOR problems with scaffolding:
    VERIFY scaffolding level matches PRE recommendation
    VERIFY scaffolding supports without creating dependence
  RESULT: Scaffolding appropriately applied ✓
```

---

## IMPLEMENTATION ROADMAP

**Phase 1: Content Framework (Week 1)**
- Create problem template schema
- Build template library for 2-3 domains
- Set up parameter generation

**Phase 2: Sequence Building (Week 2)**
- Implement sequence builder
- Add spacing/retrieval logic
- Implement interleaving selector

**Phase 3: Problem Generation (Week 2)**
- Implement problem generator
- Add parameter variation
- Create scaffolding system

**Phase 4: Cognitive Load Optimization (Week 3)**
- Implement clarity checker
- Add irrelevant detail removal
- Create formatting optimizer

**Phase 5: Integration & Testing (Week 4)**
- Connect to PRE
- Connect to Spacing System
- Performance testing
- Constitutional compliance audit

---

## TECHNICAL REQUIREMENTS

**Language:** Python 3.9+  
**Database:** PostgreSQL for templates and generated problems  
**Template Engine:** Jinja2 or similar for problem parameterization  
**Testing:** pytest with >85% coverage  
**Content:** Start with 50 templates per domain, scale to 200+

**Performance SLAs:**
- Problem generation: <300ms (p99)
- Sequence building: <500ms (p99)
- Cognitive load optimization: <200ms (p99)

---

**SPECIFICATION COMPLETE**

All three Phase 1 Foundation systems are now fully specified.

**Implementation Ready:** Yes  
**Estimated Total Effort:** 1000-1200 hours  
**Estimated Timeline:** 4-5 months with experienced team  

---

## NEXT STEPS

**For Implementation Teams:**
1. Review all three specs with engineers
2. Finalize technical architecture
3. Set up development environment
4. Begin Phase 1, Week 1: Data Layer (SIS)

**For Product/Stakeholders:**
1. Review Constitutional requirements
2. Plan user testing strategy
3. Set up success metrics aligned with Constitution
4. Plan stakeholder communication (teachers, parents, students)

**For Testing/QA:**
1. Review Observable Behaviors from Constitution
2. Create test cases for each behavior
3. Plan compliance audits
4. Set up monitoring/logging

---

All three specifications are complete and committed to GitHub.

**Ready for implementation team to begin building the foundation.**
