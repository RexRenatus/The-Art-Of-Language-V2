# Synthesis Directory

Vote aggregation and prompt synthesis components.

## Overview

The synthesis module aggregates voting results and generates optimized prompts.

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Module exports |
| `vote_aggregation.py` | Jaccard clustering, consensus calculation |
| `manager_synthesis.py` | Grok-based prompt generation |

## Vote Aggregation

### Jaccard Similarity Clustering

Groups similar suggestions from multiple voters:

```python
from prompt_optimizer.synthesis import VoteAggregator

aggregator = VoteAggregator()
result = aggregator.aggregate(ballots)

print(f"Consensus: {result.consensus}")
print(f"Approval Rate: {result.approval_rate:.0%}")
print(f"Clustered Suggestions: {len(result.clustered_suggestions)}")
```

### Algorithm

1. Calculate pairwise Jaccard similarity between suggestions
2. Cluster suggestions with similarity > threshold
3. Weight clusters by voter confidence and count
4. Select top suggestions for synthesis

### Consensus Threshold

```
Consensus = (Weighted Approvals) / (Total Weight) >= 0.57
```

- 5 of 8 voters must approve
- Pedagogy voter has weight 1.2
- Other voters have weight 1.0

## Manager Synthesis

### Grok-Based Prompt Generation

Uses Grok 4.1 with reasoning to generate optimized prompts:

```python
from prompt_optimizer.synthesis import PromptSynthesizer

synthesizer = PromptSynthesizer(openrouter_client)

prompt = await synthesizer.synthesize(
    topic_data=topic,
    voting_result=result,
    research_context=research,
)
```

### Synthesis Process

1. Analyze aggregated voting results
2. Incorporate clustered suggestions by priority
3. Apply research context from Exa
4. Generate structured prompt components:
   - Learning objectives
   - Core concepts
   - Pedagogical approach
   - Scaffolding sequence
   - Assessment strategies
   - Connections

## Configuration

Settings in `config/settings.yaml`:

```yaml
voting:
  consensus_threshold: 0.57
  min_confidence: 0.6

synthesis:
  model: "x-ai/grok-4-1"
  max_tokens: 4000
```

## See Also

- [Architecture](../docs/architecture.md)
- [Voters](../voters/README.md)
