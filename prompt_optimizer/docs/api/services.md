# Services API

The `services/` module provides API clients and processing utilities.

## Overview

```python
from prompt_optimizer.services import (
    OpenRouterClient,
    ExaClient,
    FlashcardGenerator,
    BatchProcessor,
    CostTracker,
)
```

## OpenRouterClient

Multi-model LLM access via OpenRouter API.

### Initialization

```python
from prompt_optimizer.services import OpenRouterClient

client = OpenRouterClient(api_key="sk-or-...")
# Or use OPENROUTER_API_KEY environment variable
client = OpenRouterClient()
```

### Methods

#### chat_completion

```python
async def chat_completion(
    messages: List[Dict],
    model: str = "anthropic/claude-sonnet-4",
    max_tokens: int = 2000,
    temperature: float = 0.7,
) -> str:
    """Send chat completion request."""
```

#### chat_completion_with_web

```python
async def chat_completion_with_web(
    messages: List[Dict],
    model: str = "google/gemini-2.5-flash-lite",
    max_tokens: int = 25000,
    search_context_size: str = "medium",
) -> str:
    """Chat completion with OpenRouter web plugin (Exa engine)."""
```

#### generate_flashcards_batch

```python
async def generate_flashcards_batch(
    topic_data: TopicData,
    num_flashcards: int = 15,
    research_context: Optional[str] = None,
) -> FlashcardBatch:
    """Generate flashcards for a topic."""
```

### Example

```python
import asyncio
from prompt_optimizer.services import OpenRouterClient

async def main():
    client = OpenRouterClient()

    messages = [
        {"role": "user", "content": "Explain TLS handshake"}
    ]

    response = await client.chat_completion(
        messages=messages,
        model="anthropic/claude-sonnet-4",
        max_tokens=1000,
    )
    print(response)

asyncio.run(main())
```

## ExaClient

Semantic web search via Exa API.

### Initialization

```python
from prompt_optimizer.services import ExaClient

client = ExaClient(api_key="...")
# Or use EXA_API_KEY environment variable
client = ExaClient()
```

### Methods

#### search

```python
async def search(
    query: str,
    num_results: int = 5,
    use_autoprompt: bool = True,
) -> ExaSearchResult:
    """Perform semantic search."""
```

#### search_and_contents

```python
async def search_and_contents(
    query: str,
    num_results: int = 5,
    text_length: int = 1000,
) -> ExaResearchContext:
    """Search and extract text content."""
```

### Example

```python
import asyncio
from prompt_optimizer.services import ExaClient

async def main():
    client = ExaClient()

    result = await client.search_and_contents(
        query="TLS 1.3 security improvements",
        num_results=5,
    )

    for source in result.sources:
        print(f"- {source.title}: {source.url}")

asyncio.run(main())
```

## FlashcardGenerator

Gemini-based flashcard generation with Phase 15 validation.

### Initialization

```python
from prompt_optimizer.services import FlashcardGenerator
from prompt_optimizer.services import OpenRouterClient, ExaClient

generator = FlashcardGenerator(
    openrouter_client=OpenRouterClient(),
    exa_client=ExaClient(),
)
```

### Methods

#### generate_flashcards

```python
async def generate_flashcards(
    topic_data: TopicData,
    num_flashcards: int = 15,
    use_exa: bool = True,
) -> FlashcardBatch:
    """Generate flashcards for a topic."""
```

#### generate_flashcards_with_web

```python
async def generate_flashcards_with_web(
    topic_data: TopicData,
    num_flashcards: int = 15,
    search_context_size: str = "medium",
) -> FlashcardBatch:
    """Generate flashcards using web plugin."""
```

### Example

```python
import asyncio
from prompt_optimizer.services import FlashcardGenerator, OpenRouterClient
from prompt_optimizer.models import TopicData

async def main():
    generator = FlashcardGenerator(
        openrouter_client=OpenRouterClient(),
    )

    topic = TopicData(
        topic_title="TLS Handshake",
        hierarchy=...,
    )

    batch = await generator.generate_flashcards_with_web(
        topic_data=topic,
        num_flashcards=15,
    )

    print(f"Generated {len(batch.flashcards)} flashcards")

asyncio.run(main())
```

## BatchProcessor

Checkpoint/resume for large batch processing.

### Initialization

```python
from prompt_optimizer.services import BatchProcessor, BatchConfig

config = BatchConfig(
    batch_size=100,
    batch_delay=2.0,
    max_concurrent=100,
)

processor = BatchProcessor(config)
```

### Methods

#### process_batch

```python
async def process_batch(
    topic_paths: List[str],
    process_func: Callable,
    cleanup: bool = False,
    resume: bool = False,
) -> BatchResult:
    """Process topics with checkpoint/resume."""
```

#### get_checkpoint_status

```python
def get_checkpoint_status() -> Optional[Dict]:
    """Get current checkpoint status."""
```

## CostTracker

API usage and cost monitoring.

### Initialization

```python
from prompt_optimizer.services import CostTracker

tracker = CostTracker(
    enabled=True,
    daily_limit_usd=50.0,
)
```

### Methods

#### track_usage

```python
def track_usage(
    model: str,
    input_tokens: int,
    output_tokens: int,
) -> float:
    """Track API usage and return cost."""
```

#### get_daily_usage

```python
def get_daily_usage() -> Dict[str, float]:
    """Get usage by model for today."""
```

#### check_budget

```python
def check_budget() -> bool:
    """Check if within daily budget."""
```

## RateLimiter

Token bucket rate limiting for API calls.

### Initialization

```python
from prompt_optimizer.services import RateLimiter

limiter = RateLimiter(
    requests_per_second=5,
    burst_size=10,
)
```

### Methods

#### acquire

```python
async def acquire() -> None:
    """Wait for rate limit token."""
```

## See Also

- [Voters API](voters.md)
- [Models API](models.md)
- [Architecture](../architecture.md)
