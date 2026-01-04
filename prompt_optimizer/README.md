# Prompt Optimizer

8-voter consensus-based prompt optimization system for generating topic-focused learning prompts and flashcards.

---

## Processing Progress Tracker

**Last Updated:** 2026-01-04

### Overall Progress

```
[========>---------------------------------------] 8.5% (4/47 domains)
```

| Metric | Value |
|--------|-------|
| **Domains Completed** | 4 of 47 |
| **Topics Processed** | 14,916 |
| **Flashcards Generated** | 235,049 |
| **Total Processing Time** | 8.9 hours |

---

### Category Progress Summary

| Category | Progress | Completed | Remaining |
|----------|----------|-----------|-----------|
| 001_cybersecurity | `[=====>--------------]` 21% | 4/19 | 15 |
| 002_network-engineering | `[--------------------]` 0% | 0/16 | 16 |
| 003_software-engineering | `[--------------------]` 0% | 0/12 | 12 |

---

### 001_cybersecurity (3/19 domains)

| # | Domain | Topics | Flashcards | Status |
|---|--------|-------:|----------:|:------:|
| 001 | security-and-risk-management | 5,471 | 83,021 | Done |
| 002 | asset-security | 2,597 | 39,885 | Done |
| 003 | privacy-and-data-protection | - | - | Pending |
| 004 | identity-and-access-management-iam | - | - | Pending |
| 005 | cryptography | - | - | Pending |
| 006 | communication-and-network-security | - | - | Pending |
| 007 | security-architecture-and-engineering | 3,718 | 57,847 | Done |
| 008 | software-development-security | - | - | Pending |
| 009 | application-security | - | - | Pending |
| 010 | cloud-security | - | - | Pending |
| 011 | operational-technology-ot-and-ics-security | - | - | Pending |
| 012 | security-operations | - | - | Pending |
| 013 | security-monitoring-and-analytics | - | - | Pending |
| 014 | security-assessment-and-testing | - | - | Pending |
| 015 | incident-response-and-forensics | - | - | Pending |
| 016 | penetration-testing-and-ethical-hacking | - | - | Pending |
| 017 | threat-intelligence-and-hunting | 3,130 | 54,296 | Done |
| 018 | advanced-security-architecture-and-design-patterns | - | - | Pending |
| 019 | emerging-technologies-and-trends | - | - | Pending |

**Category Subtotal:** 14,916 topics | 235,049 flashcards

---

### 002_network-engineering (0/16 domains)

| # | Domain | Topics | Flashcards | Status |
|---|--------|-------:|----------:|:------:|
| 001 | network-fundamentals | - | - | Pending |
| 002 | physical-layer-and-cabling | - | - | Pending |
| 003 | ethernet-switching-and-vlans | - | - | Pending |
| 004 | ip-addressing-and-subnetting | - | - | Pending |
| 005 | ip-routing-fundamentals | - | - | Pending |
| 006 | ospf-open-shortest-path-first | - | - | Pending |
| 007 | eigrp-enhanced-interior-gateway-routing-protocol | - | - | Pending |
| 008 | bgp-border-gateway-protocol | - | - | Pending |
| 009 | ip-multicast | - | - | Pending |
| 010 | wan-technologies-and-mpls | - | - | Pending |
| 011 | wireless-networking | - | - | Pending |
| 012 | network-services-and-infrastructure | - | - | Pending |
| 013 | network-security | - | - | Pending |
| 014 | data-center-networking | - | - | Pending |
| 015 | software-defined-networking-and-automation | - | - | Pending |
| 016 | network-troubleshooting-and-management | - | - | Pending |

**Category Subtotal:** 0 topics | 0 flashcards

---

### 003_software-engineering (0/12 domains)

| # | Domain | Topics | Flashcards | Status |
|---|--------|-------:|----------:|:------:|
| 001 | foundational-computer-science | - | - | Pending |
| 002 | system-fundamentals | - | - | Pending |
| 003 | core-software-engineering | - | - | Pending |
| 004 | project-management-and-professional-practice | - | - | Pending |
| 005 | web-development | - | - | Pending |
| 006 | mobile-application-development | - | - | Pending |
| 007 | game-development | - | - | Pending |
| 008 | data-science-and-analytics | - | - | Pending |
| 009 | artificial-intelligence-and-machine-learning | - | - | Pending |
| 010 | cloud-engineering-and-infrastructure | - | - | Pending |
| 011 | cybersecurity-and-information-security | - | - | Pending |
| 012 | specialized-domains-and-emerging-technologies | - | - | Pending |

**Category Subtotal:** 0 topics | 0 flashcards

---

### Status Legend

| Indicator | Meaning |
|-----------|---------|
| Done | Processing complete |
| Running | Currently processing |
| Pending | Not yet started |
| Failed | Requires retry |

---

## Production Run Statistics (2026-01-04)

The unified pipeline has been validated with complete production runs:

| Domain | Topics | Flashcards | Duration |
|--------|--------|------------|----------|
| 001_security-and-risk-management | 5,471 | 83,021 | 3.2 hrs |
| 002_asset-security | 2,597 | 39,885 | 1.5 hrs |
| 007_security-architecture-and-engineering | 3,718 | 57,847 | 2.2 hrs |
| 017_threat-intelligence-and-hunting | 3,130 | 54,296 | 2.1 hrs |
| **Total** | **14,916** | **235,049** | **8.9 hrs** |

**Throughput**: ~29 topics/minute average

## Features

- **8 Specialized Voter Agents** - Pedagogy, active learning, scaffolding, connection, assessment, clarity, schema, and Exa grounding
- **Web-Grounded Research** - Exa API semantic search + Context7 documentation lookup
- **Multi-Model Support** - Grok (voters/synthesis), Gemini (research/flashcards)
- **Flashcard Generation** - Gemini 2.5 Flash with 5-step distractor protocol
- **Phase 15 Validation** - 7-step synthesis validation, near-peer confusion matrix, weighted scoring
- **Database-Backed Tracking** - SQLite completion tracking with robust_connect for reliable checkpoint/resume

## Quick Start

```bash
# Install dependencies
pip install -r requirements.txt

# Set environment variables
export OPENROUTER_API_KEY="your-key"
export EXA_API_KEY="your-key"  # Optional

# Test with 3 sample topics
python -m prompt_optimizer --test

# Generate flashcards for a category
python -m prompt_optimizer --flashcards --category 001_cybersecurity --limit 10
```

## Architecture Overview

```
                              ORCHESTRATOR
                           (orchestrator.py)
                                  |
        +--------------------------+---------------------------+
        |                          |                           |
        v                          v                           v
+---------------+         +----------------+          +---------------+
|   RESEARCH    |         |    VOTING      |          |   SYNTHESIS   |
|               |         |                |          |               |
| - Exa Client  |         | - 8 Voters     |          | - Aggregation |
| - Context7    |         | - Parallel     |          | - Manager     |
| - Web Plugin  |         | - Consensus    |          | - Grok        |
+---------------+         +----------------+          +---------------+
        |                          |                           |
        +-------------+------------+---------------------------+
                      |
                      v
+---------------------------------------------------------------------+
|                              OUTPUT                                  |
|                                                                      |
| +-------------+  +-------------+  +-------------+  +-------------+  |
| |   EXPORT    |  | FLASHCARDS  |  |  DATABASE   |  |   REPORTS   |  |
| | XML/YAML/   |  |   Gemini    |  | Completion  |  | Run Summary |  |
| |   JSON      |  |             |  |  Tracking   |  |   Metrics   |  |
| +-------------+  +-------------+  +-------------+  +-------------+  |
+---------------------------------------------------------------------+
```

## Current Model Configuration

| Purpose | Model | max_tokens | Temperature |
|---------|-------|------------|-------------|
| **Voters** | x-ai/grok-4.1-fast | 2,000 | 0.7 |
| **Synthesis** | x-ai/grok-4.1-fast | 4,000 | 0.5 |
| **Research** | google/gemini-2.5-flash | 3,000 | 0.7 |
| **Flashcards** | google/gemini-2.5-flash | 50,000 | 0.1 |

## CLI Usage

### Processing Modes

| Mode | Command | Description |
|------|---------|-------------|
| **Single Topic** | `--topic path/to/topic.json` | Process one topic (dev/testing) |
| **Batch** | `--batch --category 001_cybersecurity` | Full pipeline, multiple topics |
| **Flashcards** | `--flashcards --use-web-plugin` | Fast flashcard generation |
| **Unified** | `--unified --category 001_cybersecurity` | Prompts + flashcards (recommended) |
| **Compare** | `--compare-models --limit 3` | Compare Gemini model variants |

### Common Options

```bash
# Filter by hierarchy
--category 001_cybersecurity
--domain 001_security-and-risk-management
--subdomain 001_business-continuity-planning
--limit 100

# Batch processing
--batch-size 100        # Topics per batch
--batch-delay 2.0       # Seconds between batches
--concurrent 100        # Max concurrent requests

# Processing options
--use-web-plugin        # OpenRouter web (no Exa rate limit)
--cleanup               # Delete existing output folders
--resume                # Resume from checkpoint
--force                 # Skip confirmation prompts
```

### Examples

```bash
# Production flashcard generation (recommended)
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity \
  --domain 002_security-architecture-and-engineering \
  --limit 1000 --batch-size 100 --concurrent 100 --force

# Unified pipeline with prompts + flashcards
python -m prompt_optimizer --unified \
  --category 001_cybersecurity --limit 50 \
  --batch-size 10 --concurrent 20

# Resume interrupted batch
python -m prompt_optimizer --flashcards --use-web-plugin \
  --category 001_cybersecurity --resume
```

## Configuration

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key for LLM access |
| `EXA_API_KEY` | No | Exa API key for semantic search |

### Config Files

| File | Purpose |
|------|---------|
| `config/settings.yaml` | API endpoints, models, voting thresholds |
| `config/voter_presets.yaml` | System prompts for each voter |
| `config/pedagogy_templates.yaml` | Teaching framework templates |
| `config/validation_settings.yaml` | Phase 15 validation rules |
| `config/near_peer_matrix.yaml` | Confusion matrices for distractors |

## Output Structure

Each processed topic generates:

```
topic_folder/
+-- topic.json                    # Original topic data
+-- optimized_prompt.xml          # Structured XML prompt
+-- optimized_prompt.yaml         # YAML format
+-- optimization_summary.json     # Voting results, metrics
+-- flashcards/
    +-- batch.json                # All flashcards
    +-- generation_metadata.json  # Quality scores, citations
```

## Database-Backed Completion Tracking

The system uses SQLite with `robust_connect` for reliable batch processing:

- **Automatic Checkpointing**: Progress saved after each topic
- **Crash Recovery**: Resume from exact failure point
- **Deduplication**: Skip already-completed topics
- **Error Logging**: Track failed topics with error messages

```bash
# View completion status
python -m prompt_optimizer completion-status

# List failed topics
python -m prompt_optimizer completion-failed

# Reset a topic for reprocessing
python -m prompt_optimizer completion-reset --topic path/to/topic.json
```

## Directory Structure

```
prompt_optimizer/
+-- main.py              # CLI entry point
+-- orchestrator.py      # Pipeline coordinator
+-- config/              # YAML configuration files
+-- services/            # API clients and utilities
+-- voters/              # 8 voter agent implementations
+-- synthesis/           # Vote aggregation and prompt creation
+-- export/              # File exporters (XML, YAML, JSON)
+-- models/              # Dataclasses and Pydantic models
+-- database/            # SQLite completion tracking with robust_connect
+-- utils/               # Formatting utilities (MathJax, code)
+-- run_summaries/       # Pipeline run statistics (YYYY/MM/)
```

See individual directory READMEs for detailed documentation.

## Voter Agents

| Voter | Purpose | Weight |
|-------|---------|--------|
| **PedagogyVoter** | Bloom's Taxonomy, cognitive levels | 1.2 |
| **ActiveLearningVoter** | Student engagement, discussion | 1.0 |
| **ScaffoldingVoter** | Progressive complexity | 1.0 |
| **ConnectionVoter** | Interdisciplinary links | 1.0 |
| **AssessmentVoter** | Formative assessment strategies | 1.0 |
| **ClarityVoter** | Language accessibility | 1.0 |
| **SchemaVoter** | JSON structure validation | 1.0 |
| **ExaGroundingVoter** | Fact verification, sources | 1.0 |

**Consensus Threshold**: 57% (5 of 8 voters must approve)

## Phase 15 Validation

Flashcard generation includes comprehensive validation:

- **7-Step Synthesis Validation** - Schema, domain, misconception, plausibility, originality, pattern checks
- **Near-Peer Confusion Matrix** - 500+ term groups for definition questions
- **Edit Distance Check** - Minimum 3 Levenshtein distance between distractors
- **Length Consistency** - Max 3.0 ratio between longest/shortest distractor
- **Pattern Detection** - Blocks "None/All of above" patterns
- **Acronym Expansion** - Enforces full name + acronym format
- **Parenthetical Giveaway Detection** - AUTOMATIC FAIL for revealing qualifiers

**Pass Threshold**: 80% weighted validation score

## Performance

| Metric | Value |
|--------|-------|
| Single topic (full pipeline) | ~60-90 seconds |
| Flashcards only | ~20-30 seconds |
| Unified pipeline | ~80-120 seconds |
| Throughput (web plugin, 100 concurrent) | ~29 topics/min |
| Flashcards per topic | ~15 average |

## License

Proprietary - All rights reserved.

## See Also

- [Services Documentation](services/README.md)
- [Voter Architecture](voters/README.md)
- [Configuration Reference](config/README.md)
- [Extended Documentation](docs/index.md)
