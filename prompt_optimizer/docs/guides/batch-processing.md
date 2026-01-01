# Batch Processing Guide

This guide covers large-scale topic processing with checkpoint/resume support.

## Overview

The batch processor handles:
- Processing thousands of topics efficiently
- Checkpoint/resume for interrupted runs
- Progress tracking and reporting
- Rate limiting and concurrency control

## Batch Processing Modes

### 1. Direct Exa (Default)

Uses Exa API directly for web research:

```bash
python -m prompt_optimizer --flashcards \
  --category 001_cybersecurity \
  --limit 100 \
  --batch-size 10 \
  --batch-delay 2.0
```

**Limitations**: Exa API rate limit (5 QPS)

### 2. Web Plugin (Recommended)

Uses OpenRouter web plugin (Exa engine) for higher throughput:

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --limit 1000 \
  --batch-size 100 \
  --concurrent 100
```

**Advantages**:
- No Exa rate limits
- Higher concurrency
- Faster processing

### 3. Unified Pipeline

Prompts + flashcards in one pass:

```bash
python -m prompt_optimizer --unified \
  --category 001_cybersecurity \
  --limit 100
```

## Batch Configuration

### Key Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `--batch-size` | 10 | Topics per batch |
| `--batch-delay` | 2.0 | Seconds between batches |
| `--concurrent` | 100 | Max concurrent requests |
| `--cleanup` | False | Delete existing output |
| `--resume` | False | Resume from checkpoint |
| `--force` | False | Skip confirmation |

### Recommended Settings

**Small batch (testing)**:
```bash
--limit 10 --batch-size 5 --concurrent 10
```

**Medium batch (standard)**:
```bash
--limit 100 --batch-size 20 --concurrent 50
```

**Large batch (production)**:
```bash
--limit 5000 --batch-size 100 --concurrent 100 --force
```

## Checkpoint/Resume

### How It Works

1. After each batch, progress is saved to checkpoint file
2. If interrupted, resume from last completed batch
3. Completed topics are tracked in SQLite database
4. Skips already-processed topics on resume

### Checkpoint Files

| File | Purpose |
|------|---------|
| `batch_checkpoint.json` | Direct Exa batches |
| `batch_checkpoint_web.json` | Web plugin batches |
| `batch_checkpoint_unified.json` | Unified pipeline |
| `completion_tracking.db` | SQLite completion database |

### Resume a Batch

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --resume
```

### Check Checkpoint Status

```bash
python -m prompt_optimizer --flashcards --checkpoint-status
```

Output:
```
============================================================
CHECKPOINT STATUS
============================================================
  Started: 2024-01-01 08:00:00
  Last Updated: 2024-01-01 09:30:00
  Total Topics: 1000
  Completed: 500
  Failed: 5
  Remaining: 495
  Current Batch: 6
```

## Completion Database

### View Status

```bash
python -m prompt_optimizer completion-status
```

### View Failed Topics

```bash
python -m prompt_optimizer completion-failed
```

### Reset Topic

```bash
python -m prompt_optimizer completion-reset \
  --topic path/to/topic.json
```

### Clear Database

```bash
python -m prompt_optimizer completion-clear
```

## Error Handling

### Failed Topics

Failed topics are logged and can be reprocessed:

```bash
# View failures
python -m prompt_optimizer completion-failed

# Reprocess (after fixing issues)
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --resume
```

### Database Lock Errors

With high concurrency, SQLite may show lock warnings:

```
Failed to mark topic as started in database: database is locked
```

**These are non-fatal warnings**. The processing continues, but completion tracking may be incomplete. To avoid:

- Reduce `--concurrent` to 50 or less
- Use `--batch-size` of 50 or less

### API Rate Limits

If you hit OpenRouter rate limits:

1. Reduce `--concurrent`
2. Increase `--batch-delay`
3. Monitor cost tracker output

## Duration Estimates

Approximate duration per topic:

| Mode | Duration |
|------|----------|
| Flashcards (web plugin) | ~20s |
| Flashcards (Exa) | ~30s |
| Unified | ~90s |

**Example**: 1000 topics with web plugin = ~30 min

## Performance Tuning

### Maximize Throughput

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --limit 5000 \
  --batch-size 100 \
  --concurrent 100 \
  --batch-delay 1.0 \
  --force
```

**Throughput**: ~50-100 topics/minute

### Minimize Errors

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --limit 1000 \
  --batch-size 50 \
  --concurrent 50 \
  --batch-delay 3.0
```

**Tradeoff**: Slower but more reliable

### Memory-Constrained

```bash
python -m prompt_optimizer --flashcards --use-web-plugin \
  --limit 1000 \
  --batch-size 20 \
  --concurrent 20
```

## Monitoring Progress

### Real-time Output

```
[08:19:28] Batch 1/38: Processing 100 topics... [15s elapsed]
[08:21:30]   Batch 1 complete: 100 succeeded, 0 failed
[08:21:30]   Waiting 2.0s before next batch...
[08:21:32] Batch 2/38: Processing 100 topics... [140s elapsed]
```

### Final Summary

```
============================================================
WEB PLUGIN BATCH PROCESSING COMPLETE
============================================================
  Total: 3718
  Completed: 3718
  Failed: 0
  Skipped (resumed): 0
  Duration: 4523.5s
```

## Best Practices

1. **Always use `--use-web-plugin`** for large batches
2. **Start small** - test with 10 topics first
3. **Use `--force`** for unattended runs
4. **Monitor early batches** for errors
5. **Keep checkpoints** - don't delete until verified
6. **Stagger runs** - don't overload API

## See Also

- [Getting Started](getting-started.md)
- [CLI Reference](../cli-reference.md)
- [Configuration](../configuration.md)
