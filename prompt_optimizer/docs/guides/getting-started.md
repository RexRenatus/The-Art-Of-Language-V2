# Getting Started

This guide walks you through setting up and using the Prompt Optimizer.

## Prerequisites

- Python 3.10+
- OpenRouter API key
- Exa API key (optional, for web research)

## Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd prompt_optimizer
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure Environment

Create a `.env` file in the `prompt_optimizer/` directory:

```bash
OPENROUTER_API_KEY=sk-or-v1-your-key-here
EXA_API_KEY=your-exa-key-here  # Optional
```

Or export environment variables:

```bash
export OPENROUTER_API_KEY=sk-or-v1-your-key-here
export EXA_API_KEY=your-exa-key-here
```

## Quick Start

### Test the Installation

```bash
# Run test with 3 sample topics
python -m prompt_optimizer --test
```

Expected output:
```
============================================================
PROMPT OPTIMIZER TEST RUN
============================================================
Testing with 3 topics:
  - Business Impact Analysis (BIA)
  - Recovery Point Objective (RPO)
  - Recovery Time Objective (RTO)

  ✓ Business Impact Analysis (BIA)
    Consensus: APPROVED, Files: 4
...
```

### Generate Flashcards

```bash
# Generate flashcards for 5 topics
python -m prompt_optimizer --flashcards \
  --category 001_cybersecurity \
  --limit 5
```

### Use Web Plugin (Recommended)

For higher throughput without Exa rate limits:

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --limit 10
```

## Understanding the Output

Each processed topic creates a folder structure:

```
topic_folder/
├── topic.json                    # Original topic data
├── optimized_prompt.xml          # Structured XML prompt
├── optimized_prompt.yaml         # YAML format
├── optimization_summary.json     # Voting results
└── flashcards/
    ├── batch.json                # Generated flashcards
    └── generation_metadata.json  # Quality metrics
```

### Flashcard Structure

Each flashcard in `batch.json`:

```json
{
  "question": "What is the primary purpose of TLS 1.3?",
  "correct_answer": "Secure communication with improved performance",
  "distractors": [
    {
      "text": "Backward compatibility with SSL 2.0",
      "misconception": "Targets version confusion: TLS 1.3 removed legacy support"
    }
  ],
  "explanation": "TLS 1.3 reduces handshake latency...",
  "difficulty": "intermediate",
  "bloom_level": "understand",
  "tags": ["cryptography", "protocols", "security"]
}
```

## Common Workflows

### Small Test Run

```bash
python -m prompt_optimizer --flashcards \
  --category 001_cybersecurity \
  --domain 001_security-and-risk-management \
  --limit 3
```

### Production Batch

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --limit 1000 \
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

### Check Progress

```bash
python -m prompt_optimizer --flashcards --checkpoint-status
```

## Troubleshooting

### API Key Issues

```
WARNING: OPENROUTER_API_KEY not set
```

**Solution**: Set the environment variable or create `.env` file.

### No Topics Found

```
No topics found matching criteria.
```

**Solution**: Check `--data-root` path and filter options.

### Rate Limiting

```
Rate limit exceeded
```

**Solution**: Use `--use-web-plugin` to avoid Exa rate limits, or reduce `--concurrent`.

### Database Lock Errors

```
database is locked
```

**Solution**: Reduce `--concurrent` to avoid SQLite contention, or these are non-fatal warnings that can be ignored.

## Next Steps

- [Batch Processing Guide](batch-processing.md) - Large-scale processing
- [CLI Reference](../cli-reference.md) - All command options
- [Configuration](../configuration.md) - Customize settings
- [Extending Voters](extending-voters.md) - Add custom voters

## See Also

- [Architecture Overview](../architecture.md)
- [Services API](../api/services.md)
