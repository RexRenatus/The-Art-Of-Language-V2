# Services Directory

API clients and processing utilities for the Prompt Optimizer.

## Overview

The services module provides external API integration and core processing utilities.

## Components

| File | Purpose |
|------|---------|
| `openrouter_client.py` | Multi-model LLM access via OpenRouter |
| `exa_client.py` | Semantic web search via Exa API |
| `context7_client.py` | Technical documentation lookup |
| `flashcard_generator.py` | Gemini-based flashcard creation |
| `flashcard_quality_evaluator.py` | Phase 15 validation |
| `batch_processor.py` | Checkpoint/resume batch processing |
| `rate_limiter.py` | Token bucket rate limiting |
| `retry_handler.py` | Exponential backoff retry logic |
| `cost_tracker.py` | API usage and cost monitoring |
| `run_summary_repository.py` | After-action run reports |
| `gemini_flashcard_comparer.py` | Compare Gemini model variants |
| `web_research_adapter.py` | Exa integration layer |

## Key Services

### OpenRouterClient

Multi-model LLM access supporting Claude, GPT-4, Gemini, and Grok.

```python
from prompt_optimizer.services import OpenRouterClient

client = OpenRouterClient()

# Standard completion
response = await client.chat_completion(
    messages=[{"role": "user", "content": "..."}],
    model="anthropic/claude-sonnet-4",
)

# With web plugin (no rate limits)
response = await client.chat_completion_with_web(
    messages=[...],
    search_context_size="medium",
)
```

### FlashcardGenerator

Generates flashcards with 5-step distractor protocol and Phase 15 validation.

```python
from prompt_optimizer.services import FlashcardGenerator

generator = FlashcardGenerator(openrouter_client)

batch = await generator.generate_flashcards_with_web(
    topic_data=topic,
    num_flashcards=15,
)
```

### BatchProcessor

Handles large batch processing with checkpoint/resume.

```python
from prompt_optimizer.services import BatchProcessor, BatchConfig

processor = BatchProcessor(BatchConfig(
    batch_size=100,
    max_concurrent=100,
))

result = await processor.process_batch(
    topic_paths=topics,
    process_func=process_topic,
    resume=True,
)
```

### CostTracker

Monitors API usage and costs.

```python
from prompt_optimizer.services import CostTracker

tracker = CostTracker(daily_limit_usd=50.0)
cost = tracker.track_usage("gpt-4o", input_tokens=1000, output_tokens=500)
```

## Configuration

Services read configuration from:
- Environment variables (`OPENROUTER_API_KEY`, `EXA_API_KEY`)
- `config/settings.yaml`

## See Also

- [Services API](../docs/api/services.md)
- [Configuration](../docs/configuration.md)
