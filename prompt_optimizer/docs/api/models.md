# Models API

The `models/` module provides type-safe data structures for the prompt optimizer.

## Overview

```python
from prompt_optimizer.models import (
    TopicData,
    TopicHierarchy,
    VoterBallot,
    VotingResult,
    OptimizedPrompt,
    FlashcardBatch,
    Flashcard,
    ExaResearchContext,
)
```

## Core Data Classes

### TopicData

Complete topic metadata from topic.json files.

```python
@dataclass
class TopicData:
    topic_title: str
    hierarchy: TopicHierarchy
    entry_domain: Optional[str] = None
    entry_subdomain: Optional[str] = None
    metadata: Optional[Dict] = None
```

### TopicHierarchy

6-level curriculum structure.

```python
@dataclass
class TopicHierarchy:
    level_1_category: str      # e.g., "Cybersecurity"
    level_2_domain: str        # e.g., "Security Architecture"
    level_3_subdomain: str     # e.g., "Cloud Service Models"
    level_4_entry_domain: str  # e.g., "Cross-Model Security"
    level_5_entry_subdomain: str  # e.g., "Business Continuity"
    level_6_topic: str         # e.g., "Backup Encryption"
```

### Example

```python
from prompt_optimizer.models import TopicData, TopicHierarchy

hierarchy = TopicHierarchy(
    level_1_category="Cybersecurity",
    level_2_domain="Security Architecture And Engineering",
    level_3_subdomain="Cloud Service Models",
    level_4_entry_domain="Cross-Model Security Controls",
    level_5_entry_subdomain="Business Continuity and Disaster Recovery",
    level_6_topic="Backup Encryption and Storage",
)

topic = TopicData(
    topic_title="Backup Encryption and Storage",
    hierarchy=hierarchy,
)
```

## Voting Data Classes

### VoterBallot

Single voter's evaluation result.

```python
@dataclass
class VoterBallot:
    voter_name: str
    decision: str  # APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, REJECT
    confidence: float  # 0.0 - 1.0
    scores: Dict[str, float]  # overall, clarity, pedagogy, accuracy, completeness
    suggestions: List[Suggestion]
    reasoning: str
    weight: float = 1.0
```

### Suggestion

Individual improvement suggestion.

```python
@dataclass
class Suggestion:
    content: str
    priority: str  # HIGH, MEDIUM, LOW
    category: str  # e.g., "pedagogy", "clarity", "structure"
```

### VotingResult

Aggregated voting results.

```python
@dataclass
class VotingResult:
    ballots: List[VoterBallot]
    consensus: str  # APPROVED, NEEDS_REVISION, REJECTED
    approval_rate: float
    clustered_suggestions: List[SuggestionCluster]
    weighted_scores: Dict[str, float]
```

## Prompt Data Classes

### OptimizedPrompt

Final synthesized prompt.

```python
@dataclass
class OptimizedPrompt:
    topic: str
    hierarchy: TopicHierarchy
    learning_objectives: List[str]
    core_concepts: List[str]
    pedagogical_approach: str
    scaffolding_sequence: List[str]
    assessment_strategies: List[str]
    connections: Dict[str, List[str]]
    metadata: Dict
```

## Flashcard Data Classes

### Flashcard

Single flashcard with distractors.

```python
@dataclass
class Flashcard:
    question: str
    correct_answer: str
    distractors: List[Distractor]
    explanation: str
    difficulty: str  # foundational, beginner, intermediate, advanced, master
    bloom_level: str  # remember, understand, apply, analyze, evaluate, create
    tags: List[str]
```

### Distractor

Flashcard distractor with misconception.

```python
@dataclass
class Distractor:
    text: str
    misconception: str  # "Targets [type]: reasoning"
```

### FlashcardBatch

Collection of flashcards with metadata.

```python
@dataclass
class FlashcardBatch:
    topic: str
    flashcards: List[Flashcard]
    generation_metadata: GenerationMetadata
    quality_metrics: QualityMetrics
```

### QualityMetrics

Phase 15 validation metrics.

```python
@dataclass
class QualityMetrics:
    distractor_quality_score: float
    originality_compliance: float
    near_peer_utilization: float
    misconception_coverage: float
    validation_pass_rate: float
    weighted_validation_score: float
    edit_distance_passed: bool
    pattern_detection_passed: bool
    automatic_fails: List[str]
```

## Research Data Classes

### ExaResearchContext

Web research results from Exa.

```python
@dataclass
class ExaResearchContext:
    query: str
    sources: List[ExaSource]
    summary: str
    retrieved_at: datetime
```

### ExaSource

Individual source from Exa search.

```python
@dataclass
class ExaSource:
    title: str
    url: str
    text: str
    score: float
    published_date: Optional[datetime]
```

## Pydantic Models

For API validation, Pydantic models are available:

```python
from prompt_optimizer.models.pydantic_models import (
    TopicDataModel,
    FlashcardModel,
    FlashcardBatchModel,
)

# Validate JSON data
data = FlashcardBatchModel.model_validate(json_data)
```

## Usage Examples

### Loading Topic Data

```python
import json
from prompt_optimizer.models import TopicData, TopicHierarchy

with open("path/to/topic.json") as f:
    data = json.load(f)

hierarchy = TopicHierarchy(**data["hierarchy"])
topic = TopicData(
    topic_title=data["topic_title"],
    hierarchy=hierarchy,
    entry_domain=data.get("entry_domain"),
    entry_subdomain=data.get("entry_subdomain"),
)
```

### Creating Flashcards

```python
from prompt_optimizer.models import Flashcard, Distractor, FlashcardBatch

distractor1 = Distractor(
    text="SSL 2.0",
    misconception="Targets version confusion: conflates deprecated SSL with current TLS",
)

flashcard = Flashcard(
    question="What is the current recommended version of TLS?",
    correct_answer="TLS 1.3",
    distractors=[distractor1, ...],
    explanation="TLS 1.3 was standardized in 2018...",
    difficulty="intermediate",
    bloom_level="remember",
    tags=["cryptography", "protocols"],
)

batch = FlashcardBatch(
    topic="TLS Protocol Versions",
    flashcards=[flashcard, ...],
    generation_metadata=...,
    quality_metrics=...,
)
```

## See Also

- [Services API](services.md)
- [Voters API](voters.md)
- [Models Directory README](../../models/README.md)
