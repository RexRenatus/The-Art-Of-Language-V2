# Models Directory

Type-safe data structures for the Prompt Optimizer.

## Overview

Provides dataclasses and Pydantic models for type safety and validation.

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Module exports |
| `dataclasses.py` | Core data structures |
| `pydantic_models.py` | Pydantic schemas for validation |

## Core Data Classes

### TopicData

Complete topic metadata:

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

6-level curriculum structure:

```python
@dataclass
class TopicHierarchy:
    level_1_category: str      # Cybersecurity
    level_2_domain: str        # Security Architecture
    level_3_subdomain: str     # Cloud Service Models
    level_4_entry_domain: str  # Cross-Model Security
    level_5_entry_subdomain: str  # Business Continuity
    level_6_topic: str         # Backup Encryption
```

### VoterBallot

Single voter's evaluation:

```python
@dataclass
class VoterBallot:
    voter_name: str
    decision: str  # APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, REJECT
    confidence: float
    scores: Dict[str, float]
    suggestions: List[Suggestion]
    reasoning: str
    weight: float = 1.0
```

### Flashcard

Single flashcard:

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

### FlashcardBatch

Collection with metadata:

```python
@dataclass
class FlashcardBatch:
    topic: str
    flashcards: List[Flashcard]
    generation_metadata: GenerationMetadata
    quality_metrics: QualityMetrics
```

## Usage

```python
from prompt_optimizer.models import (
    TopicData,
    TopicHierarchy,
    Flashcard,
    FlashcardBatch,
)

# Create topic
hierarchy = TopicHierarchy(
    level_1_category="Cybersecurity",
    level_2_domain="Application Security",
    level_3_subdomain="Web Security",
    level_4_entry_domain="Injection Attacks",
    level_5_entry_subdomain="SQL Injection",
    level_6_topic="Prevention Techniques",
)

topic = TopicData(
    topic_title="SQL Injection Prevention",
    hierarchy=hierarchy,
)
```

## Pydantic Models

For API validation:

```python
from prompt_optimizer.models.pydantic_models import (
    FlashcardModel,
    FlashcardBatchModel,
)

# Validate JSON
data = FlashcardBatchModel.model_validate(json_data)
```

## See Also

- [Models API](../docs/api/models.md)
- [Services](../services/README.md)
