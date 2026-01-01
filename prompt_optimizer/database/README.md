# Database Directory

SQLite-based completion tracking for batch processing with robust_connect for reliable operation.

## Overview

Tracks processing status for checkpoint/resume functionality. Validated at scale with 11,786 topics processed in the 2026-01-01 production run.

## robust_connect

The database uses `robust_connect` for reliable SQLite operations:

- **Automatic Retry**: Handles transient lock errors gracefully
- **WAL Mode**: Write-Ahead Logging for concurrent access
- **Busy Timeout**: Wait for locks instead of immediate failure
- **Connection Pooling**: Efficient connection reuse

```python
from prompt_optimizer.database import robust_connect

# Automatically handles locking and retries
with robust_connect() as conn:
    cursor = conn.execute("SELECT * FROM topic_completions")
    results = cursor.fetchall()
```

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Module exports |
| `models.py` | CompletionStatus, TopicCompletion, robust_connect |
| `completion_database.py` | SQLite database operations |
| `migrate_checkpoint.py` | JSON to SQLite migration |
| `cli_commands.py` | Database CLI subcommands |

## Completion Status

```python
class CompletionStatus(Enum):
    PENDING = "pending"
    COMPLETED = "completed"
    FAILED = "failed"
    SKIPPED = "skipped"
```

## Database Schema

```sql
CREATE TABLE topic_completions (
    id INTEGER PRIMARY KEY,
    topic_path TEXT UNIQUE,
    content_hash TEXT,
    status TEXT,
    started_at DATETIME,
    completed_at DATETIME,
    duration_seconds REAL,
    flashcard_count INTEGER,
    cost_usd REAL,
    error_message TEXT
);

CREATE INDEX idx_status ON topic_completions(status);
CREATE INDEX idx_completed_at ON topic_completions(completed_at);
```

## CLI Commands

```bash
# Show database status
python -m prompt_optimizer completion-status

# Show failed topics
python -m prompt_optimizer completion-failed

# Reset topic status
python -m prompt_optimizer completion-reset --topic path/to/topic.json

# Clear all records
python -m prompt_optimizer completion-clear

# Export completion report
python -m prompt_optimizer completion-export --output report.json
```

## Usage in Code

```python
from prompt_optimizer.database import CompletionDatabase, CompletionStatus

db = CompletionDatabase()

# Check if topic completed
if db.is_completed("path/to/topic.json"):
    print("Already processed")

# Mark as completed
db.mark_completed(
    topic_path="path/to/topic.json",
    flashcard_count=15,
    cost_usd=0.031,
)

# Mark as failed
db.mark_failed(
    topic_path="path/to/topic.json",
    error_message="API error",
)

# Get completion statistics
stats = db.get_stats()
print(f"Completed: {stats['completed']}, Failed: {stats['failed']}")
```

## Database Configuration

Default settings in `config/settings.yaml`:

```yaml
database:
  path: "completion_tracking.db"
  connection_timeout: 30
  busy_timeout: 5000
  journal_mode: "WAL"
  synchronous: "NORMAL"
```

## Database Location

Default: `prompt_optimizer/completion_tracking.db`

## Migration

Migrate from JSON checkpoints:

```python
from prompt_optimizer.database import migrate_checkpoint

migrate_checkpoint("batch_checkpoint.json")
```

## Production Statistics

From the 2026-01-01 validation run:

| Metric | Value |
|--------|-------|
| Topics processed | 11,786 |
| Successful completions | 11,786 |
| Failed topics | 0 |
| Total flashcards | 180,753 |
| Average per topic | 15.3 |

## See Also

- [Batch Processing Guide](../docs/guides/batch-processing.md)
- [CLI Reference](../docs/cli-reference.md)
- [Configuration Reference](../docs/configuration.md)
