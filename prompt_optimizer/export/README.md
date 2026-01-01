# Export Directory

File exporters for optimized prompts and flashcards.

## Overview

The export module saves generated content to topic folders in multiple formats.

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Module exports |
| `topic_exporter.py` | XML/YAML/JSON prompt export |
| `flashcard_exporter.py` | Flashcard folder structure |
| `comparison_report_exporter.py` | Model comparison reports |
| `xml_generator.py` | XML serialization |
| `yaml_wrapper.py` | YAML serialization |

## Output Structure

Each processed topic generates:

```
topic_folder/
├── topic.json                    # Original topic data
├── optimized_prompt.xml          # Structured XML prompt
├── optimized_prompt.yaml         # YAML format
├── optimization_summary.json     # Voting results, metrics
└── flashcards/
    ├── batch.json                # All flashcards
    └── generation_metadata.json  # Quality metrics
```

## Topic Exporter

Exports optimized prompts to XML, YAML, and JSON:

```python
from prompt_optimizer.export import TopicExporter

exporter = TopicExporter()

files = exporter.export(
    topic_path="path/to/topic.json",
    optimized_prompt=prompt,
    voting_result=result,
)
```

### XML Format

```xml
<?xml version="1.0" encoding="UTF-8"?>
<optimized_prompt>
  <topic>TLS Handshake</topic>
  <learning_objectives>
    <objective>Explain the TLS handshake process</objective>
  </learning_objectives>
  <core_concepts>
    <concept>Key exchange</concept>
  </core_concepts>
</optimized_prompt>
```

### YAML Format

```yaml
topic: TLS Handshake
learning_objectives:
  - Explain the TLS handshake process
core_concepts:
  - Key exchange
```

## Flashcard Exporter

Exports flashcards to JSON with metadata:

```python
from prompt_optimizer.export import FlashcardExporter

exporter = FlashcardExporter()

files = exporter.export(
    topic_path="path/to/topic.json",
    flashcard_batch=batch,
)
```

### Flashcard JSON Structure

```json
{
  "topic": "TLS Handshake",
  "flashcards": [
    {
      "question": "What is the first step in TLS handshake?",
      "correct_answer": "ClientHello message",
      "distractors": [
        {
          "text": "ServerHello message",
          "misconception": "Targets order confusion: server responds, not initiates"
        }
      ],
      "explanation": "The client initiates...",
      "difficulty": "intermediate",
      "bloom_level": "understand",
      "tags": ["cryptography", "protocols"]
    }
  ],
  "generation_metadata": {
    "model": "google/gemini-2.5-flash-lite",
    "generated_at": "2024-01-01T12:00:00Z"
  },
  "quality_metrics": {
    "distractor_quality_score": 0.95,
    "validation_pass_rate": 0.92
  }
}
```

## Comparison Report Exporter

Exports model comparison results:

```python
from prompt_optimizer.export import ComparisonReportExporter

exporter = ComparisonReportExporter()

exporter.export(
    comparison_results=results,
    output_dir="comparison_reports/",
)
```

## Configuration

Settings in `config/settings.yaml`:

```yaml
export:
  formats:
    - xml
    - yaml
    - json
  create_flashcard_folder: true
  include_metadata: true
```

## See Also

- [CLI Reference](../docs/cli-reference.md)
- [Models API](../docs/api/models.md)
