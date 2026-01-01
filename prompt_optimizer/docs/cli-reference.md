# CLI Reference

The Prompt Optimizer CLI provides multiple modes for processing topics and generating flashcards.

## Usage

```bash
python -m prompt_optimizer [MODE] [OPTIONS]
```

## Modes

### Single Topic

Process a single topic file:

```bash
python -m prompt_optimizer --topic path/to/topic.json
```

**Options:**
- `--no-comparison` - Skip multi-model comparison
- `--quiet` - Suppress progress output

### Batch Mode

Process multiple topics with full pipeline:

```bash
python -m prompt_optimizer --batch --category 001_cybersecurity --limit 50
```

**Options:**
- `--category` - Filter by category folder
- `--domain` - Filter by domain folder
- `--subdomain` - Filter by subdomain folder
- `--limit` - Maximum topics to process
- `--concurrent` - Max concurrent requests (default: 100)
- `--no-comparison` - Skip model comparison

### Flashcard Mode

Generate flashcards only (faster):

```bash
python -m prompt_optimizer --flashcards --use-web-plugin --category 001_cybersecurity
```

**Options:**
- `--num-flashcards` - Cards per topic (default: 15)
- `--use-web-plugin` - Use OpenRouter web instead of Exa
- `--no-exa` - Skip all web research
- `--search-context-size` - low/medium/high (default: medium)
- `--batch-size` - Topics per batch (default: 10)
- `--batch-delay` - Seconds between batches (default: 2.0)
- `--cleanup` - Delete existing flashcard folders
- `--resume` - Resume from checkpoint
- `--checkpoint-status` - Show checkpoint status and exit

### Unified Mode

Prompts + flashcards in one pass (recommended for production):

```bash
python -m prompt_optimizer --unified --category 001_cybersecurity --limit 100
```

**Options:**
- All flashcard mode options
- Automatically uses web plugin

### Compare Models

Compare Gemini model variants:

```bash
python -m prompt_optimizer --compare-models --category 001_cybersecurity --limit 3
```

### Test Modes

```bash
# Test full pipeline with 3 sample topics
python -m prompt_optimizer --test

# Test flashcard generation
python -m prompt_optimizer --test-flashcards
```

## Common Options

### Filtering

```bash
--category 001_cybersecurity              # Category folder
--domain 001_security-and-risk-management # Domain folder
--subdomain 001_business-continuity       # Subdomain folder
--limit 100                               # Max topics
```

### Batch Processing

```bash
--batch-size 100    # Topics per batch
--batch-delay 2.0   # Seconds between batches
--concurrent 100    # Max concurrent requests
--cleanup           # Delete existing output
--resume            # Resume from checkpoint
--force             # Skip confirmation prompts
```

### Output

```bash
--quiet             # Suppress progress output
--data-root ./Data  # Root data directory
```

## Examples

### Production Flashcard Generation

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --domain 002_security-architecture-and-engineering \
  --limit 5000 \
  --batch-size 100 \
  --concurrent 100 \
  --force
```

### Resume Interrupted Batch

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --resume
```

### Check Checkpoint Status

```bash
python -m prompt_optimizer --flashcards --checkpoint-status
```

### Small Test Run

```bash
python -m prompt_optimizer --flashcards \
  --category 001_cybersecurity \
  --limit 3 \
  --no-exa
```

## Completion Database Commands

Manage the SQLite completion tracking database:

```bash
# Show database status
python -m prompt_optimizer completion-status

# Show failed topics
python -m prompt_optimizer completion-failed

# Reset topic status
python -m prompt_optimizer completion-reset --topic path/to/topic.json

# Clear all records
python -m prompt_optimizer completion-clear
```

## Output Files

Each processed topic generates:

```
topic_folder/
├── topic.json                    # Original topic
├── optimized_prompt.xml          # XML prompt
├── optimized_prompt.yaml         # YAML prompt
├── optimization_summary.json     # Voting results
└── flashcards/
    ├── batch.json                # Flashcards
    └── generation_metadata.json  # Metadata
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key |
| `EXA_API_KEY` | No | Exa API key |

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | Error (check output) |

## See Also

- [Getting Started](guides/getting-started.md)
- [Batch Processing Guide](guides/batch-processing.md)
- [Configuration](configuration.md)
