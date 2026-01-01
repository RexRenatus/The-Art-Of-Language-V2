# System Architecture

The Prompt Optimizer uses a multi-agent consensus architecture to generate high-quality educational prompts and flashcards.

## Production Validation (2026-01-01)

The architecture has been validated at scale:
- **11,786 topics** processed across 3 domains
- **180,753 flashcards** generated
- **6.8 hours** total runtime
- **29 topics/minute** average throughput

## High-Level Overview

```
+-----------------------------------------------------------------------------+
|                              ORCHESTRATOR                                    |
|                           (orchestrator.py)                                  |
|                                                                              |
|  Coordinates all components and manages the processing pipeline              |
+-----------------------------------------------------------------------------+
                                    |
        +---------------------------+---------------------------+
        |                           |                           |
        v                           v                           v
+---------------+          +---------------+          +---------------+
|   RESEARCH    |          |    VOTING     |          |   SYNTHESIS   |
|               |          |               |          |               |
| - Exa API     |          | - 8 Voters    |          | - Aggregation |
| - Context7    |          | - Parallel    |          | - Manager     |
| - Web Plugin  |          | - Consensus   |          | - Grok        |
+---------------+          +---------------+          +---------------+
        |                           |                           |
        +---------------------------+---------------------------+
                                    |
                                    v
+-----------------------------------------------------------------------------+
|                                OUTPUT                                        |
|                                                                              |
|  +-------------+    +-------------+    +-------------+    +-------------+   |
|  |   EXPORT    |    | FLASHCARDS  |    |  DATABASE   |    |   REPORTS   |   |
|  | XML/YAML/   |    | Gemini 2.5  |    | Completion  |    | Run Summary |   |
|  |   JSON      |    |   Flash     |    |  Tracking   |    |   Metrics   |   |
|  +-------------+    +-------------+    +-------------+    +-------------+   |
+-----------------------------------------------------------------------------+
```

## Current Model Configuration

| Component | Model | max_tokens | Purpose |
|-----------|-------|------------|---------|
| **Voters** | x-ai/grok-4.1-fast | 2,000 | Cost-efficient parallel evaluation |
| **Synthesis** | x-ai/grok-4.1-fast | 4,000 | Manager prompt with reasoning |
| **Research** | google/gemini-2.5-flash | 3,000 | Web grounding and search |
| **Flashcards** | google/gemini-2.5-flash | 50,000 | High-capacity batch generation |

## Processing Pipelines

### Pipeline 1: Full Optimization

For single topics requiring complete prompt optimization:

```
Topic JSON
    |
    v
[1] Load Topic Data
    |
    v
[2] Research with Exa/Web Plugin
    |   - Semantic search for sources
    |   - Extract relevant content
    |   - Build research context
    |
    v
[3] Run 8 Voter Evaluations (parallel)
    |   - PedagogyVoter
    |   - ActiveLearningVoter
    |   - ScaffoldingVoter
    |   - ConnectionVoter
    |   - AssessmentVoter
    |   - ClarityVoter
    |   - SchemaVoter
    |   - ExaGroundingVoter
    |
    v
[4] Aggregate Votes
    |   - Jaccard clustering of suggestions
    |   - Weighted consensus calculation
    |   - 57% threshold (5 of 8 voters)
    |
    v
[5] Synthesize Optimized Prompt
    |   - Manager synthesis with Grok
    |   - Incorporate consensus suggestions
    |   - Generate final prompt structure
    |
    v
[6] Model Comparison (optional)
    |   - Claude, GPT-4o, Gemini, Grok
    |   - Quality scoring
    |
    v
[7] Export to Topic Folder
    |
    v
XML + YAML + JSON outputs
```

**Duration**: ~60-90 seconds per topic

### Pipeline 2: Flashcard Generation

Fast flashcard generation without full prompt optimization:

```
Topic JSON
    |
    v
Load Topic Data
    |
    v
Research with Exa/Web Plugin (optional)
    |
    v
Generate Flashcards (Gemini 2.5 Flash)
    |   - 5-step distractor protocol
    |   - Phase 15 validation
    |   - 15 flashcards per topic
    |   - max_tokens: 50000
    |
    v
Export to flashcards/ folder
    |
    v
JSON flashcards + metadata
```

**Duration**: ~20-30 seconds per topic

### Pipeline 3: Unified (Production)

Combined prompts + flashcards in one pass:

```
Topic JSON
    |
    v
[1] Load topic
    |
    v
[2] Research with web plugin
    |
    v
[3-6] Full prompt optimization
    |
    v
[7] Generate flashcards
    |
    v
[8] Export all outputs
    |
    v
[9] Update completion database
```

**Duration**: ~80-120 seconds per topic
**Throughput**: ~29 topics/minute with web plugin

## Component Details

### Orchestrator

The central coordinator (`orchestrator.py`) manages:

- Topic discovery and loading
- Research client coordination
- Voter agent parallelization
- Vote aggregation
- Prompt synthesis
- Export operations
- Batch processing with database-backed checkpoints

### Voter Agents

8 specialized agents evaluate prompts from different perspectives:

| Voter | Focus | Weight |
|-------|-------|--------|
| PedagogyVoter | Bloom's Taxonomy, cognitive levels | 1.2 |
| ActiveLearningVoter | Student engagement, discussion | 1.0 |
| ScaffoldingVoter | Progressive complexity | 1.0 |
| ConnectionVoter | Interdisciplinary links | 1.0 |
| AssessmentVoter | Formative assessment | 1.0 |
| ClarityVoter | Language accessibility | 1.0 |
| SchemaVoter | Structure validation | 1.0 |
| ExaGroundingVoter | Fact verification | 1.0 |

### Vote Aggregation

Uses Jaccard similarity clustering to group related suggestions:

1. Calculate similarity between all suggestion pairs
2. Cluster suggestions with similarity > threshold
3. Weight clusters by voter confidence and count
4. Select top suggestions for synthesis

**Consensus Threshold**: 57% (5 of 8 voters must approve)

### Synthesis

Manager synthesis uses Grok with reasoning to:

1. Analyze aggregated voting results
2. Incorporate clustered suggestions
3. Generate structured prompt components
4. Ensure pedagogical quality

## Database-Backed Completion Tracking

### robust_connect Architecture

The system uses SQLite with `robust_connect` for reliable operation:

```
+-------------------+     +------------------+     +------------------+
|   Orchestrator    | --> | robust_connect   | --> |  SQLite (WAL)    |
|                   |     |                  |     |                  |
| - Process topic   |     | - Auto retry     |     | - Completion DB  |
| - Mark complete   |     | - Busy timeout   |     | - Run summaries  |
| - Handle errors   |     | - Lock handling  |     | - Error logs     |
+-------------------+     +------------------+     +------------------+
```

### Features

- **WAL Mode**: Write-Ahead Logging for concurrent reads/writes
- **Automatic Retry**: Handles transient lock errors gracefully
- **Busy Timeout**: Waits for locks instead of failing immediately
- **Atomic Updates**: Each topic completion is an atomic transaction

### Schema

```sql
CREATE TABLE topic_completions (
    id INTEGER PRIMARY KEY,
    topic_path TEXT UNIQUE,
    content_hash TEXT,
    status TEXT,           -- pending/completed/failed/skipped
    started_at DATETIME,
    completed_at DATETIME,
    duration_seconds REAL,
    flashcard_count INTEGER,
    cost_usd REAL,
    error_message TEXT
);
```

## Data Flow

```
External APIs                     Internal Components                Output Files
-------------                     -------------------                ------------

OpenRouter API ------+
                     |
Exa API -------------+-------> Orchestrator -------> Topic Exporter ------> .xml/.yaml/.json
                     |              |
Context7 ------------+              |
                                    |
                                    +-------> Flashcard Generator ------> flashcards/
                                    |
                                    +-------> Completion Database ------> .db
                                    |
                                    +-------> Run Summary ------> run_summaries/
```

## Async Architecture

### Concurrency Model

- **Voters**: Run in parallel (all 8 simultaneously)
- **Batches**: Process serially with configurable batch size
- **Within batch**: Controlled concurrency (default 100)
- **Rate limiting**: Token bucket for Exa API (5 QPS)

### Checkpoint/Resume with robust_connect

For large batch processing:

1. Save progress after each batch to SQLite
2. Track completed/failed topics with atomic updates
3. Resume from last checkpoint on restart
4. Skip already-completed topics automatically
5. Retry failed topics on subsequent runs

## Configuration

### Key Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `voting.consensus_threshold` | 0.57 | 5 of 8 voters |
| `voting.min_confidence` | 0.6 | Minimum voter confidence |
| `flashcard.max_tokens` | 50000 | Token limit for generation |
| `batch.size` | 10 | Topics per batch |
| `batch.concurrent` | 100 | Max concurrent requests |

### Environment Variables

- `OPENROUTER_API_KEY` - Required for LLM access
- `EXA_API_KEY` - Optional for semantic search

## Production Metrics

From the 2026-01-01 validation run:

| Domain | Topics | Flashcards | Duration |
|--------|--------|------------|----------|
| security-and-risk-management | 5,471 | 83,021 | 3.2 hrs |
| security-architecture-and-engineering | 3,718 | 57,847 | 2.2 hrs |
| asset-security | 2,597 | 39,885 | 1.5 hrs |
| **Total** | **11,786** | **180,753** | **6.8 hrs** |

## See Also

- [Services API](api/services.md)
- [Voters API](api/voters.md)
- [Configuration Reference](configuration.md)
