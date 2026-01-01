# Configuration Reference

The Prompt Optimizer uses YAML configuration files located in the `config/` directory.

## Configuration Files

| File | Purpose |
|------|---------|
| `settings.yaml` | Core settings: API, models, voting, export |
| `voter_presets.yaml` | System prompts for voter agents |
| `pedagogy_templates.yaml` | Teaching framework templates |
| `validation_settings.yaml` | Phase 15 validation rules |
| `near_peer_matrix.yaml` | Confusion matrices for distractors |
| `misconceptions_cyber.yaml` | Domain-specific misconceptions |
| `output_schema.yaml` | Export schema definitions |

## Current Model Configuration (Production)

As validated in the 2026-01-01 production run (180,753 flashcards generated):

| Purpose | Model | max_tokens | Temperature | Notes |
|---------|-------|------------|-------------|-------|
| **Voters** | x-ai/grok-4.1-fast | 2,000 | 0.7 | Cost-efficient evaluation |
| **Synthesis** | x-ai/grok-4.1-fast | 4,000 | 0.5 | Reasoning enabled |
| **Research** | google/gemini-2.5-flash | 3,000 | 0.7 | Web grounding |
| **Flashcards** | google/gemini-2.5-flash | 50,000 | 0.1 | High token limit for batch generation |

## settings.yaml

### API Configuration

```yaml
api:
  openrouter:
    base_url: "https://openrouter.ai/api/v1"
    timeout: 120
    max_retries: 3
  exa:
    base_url: "https://api.exa.ai"
    timeout: 30
    max_results: 10
```

### Model Configuration

```yaml
models:
  # Voter model (Grok for cost-efficiency)
  voter:
    model_id: "x-ai/grok-4.1-fast"
    max_tokens: 2000
    temperature: 0.7

  # Synthesis model (Grok with reasoning)
  synthesis:
    model_id: "x-ai/grok-4.1-fast"
    max_tokens: 4000
    temperature: 0.5
    reasoning:
      enabled: true

  # Flashcard generation (Gemini with high token limit)
  flashcards:
    model_id: "google/gemini-2.5-flash"
    max_tokens: 50000
    temperature: 0.1

  # Comparison models
  comparison:
    claude:
      model_id: "anthropic/claude-sonnet-4"
      temperature: 0.7
      max_tokens: 3000
    gpt4o:
      model_id: "openai/gpt-4o"
      temperature: 0.7
      max_tokens: 3000
    gemini:
      model_id: "google/gemini-2.5-flash"
      temperature: 0.7
      max_tokens: 3000
    grok:
      model_id: "x-ai/grok-4.1-fast"
      temperature: 0.7
      max_tokens: 3000
```

### Voting Configuration

```yaml
voting:
  consensus_threshold: 0.57  # 5 of 8 voters
  min_confidence: 0.6
  weights:
    pedagogy: 1.2
    active_learning: 1.0
    scaffolding: 1.0
    connection: 1.0
    assessment: 1.0
    clarity: 1.0
    schema: 1.0
    exa_grounding: 1.0
```

### Export Configuration

```yaml
export:
  formats:
    - xml
    - yaml
    - json
  create_flashcard_folder: true
  include_metadata: true
```

## Database Configuration

The system uses SQLite with `robust_connect` for reliable completion tracking:

```yaml
database:
  path: "completion_tracking.db"
  connection_timeout: 30
  busy_timeout: 5000
  journal_mode: "WAL"
  synchronous: "NORMAL"
```

### robust_connect Features

- **Automatic Retry**: Handles transient SQLite lock errors
- **WAL Mode**: Write-Ahead Logging for concurrent access
- **Busy Timeout**: Wait for locks instead of immediate failure
- **Connection Pooling**: Efficient connection reuse

## validation_settings.yaml

### Phase 15 Validation

```yaml
validation:
  pass_threshold: 0.80  # 80% weighted score required

  weights:
    prohibited_phrases: 1.0
    self_contained: 0.5
    distractor_diversity: 1.0
    distractor_reasoning: 1.0
    distractor_length_consistency: 0.5
    distractor_edit_distance: 0.5
    domain_coherence: 0.8
    required_fields: 1.0
    answer_quality: 1.0
    parenthetical_giveaways: 1.0  # AUTOMATIC FAIL
    pattern_detection: 0.8
    bloom_level_present: 0.5

  severity_mapping:
    prohibited_phrases: error
    distractor_diversity: error
    parenthetical_giveaways: error
    self_contained: warning
```

### Blocked Patterns

```yaml
blocked_patterns:
  - "none of the above"
  - "all of the above"
  - "both [A-D] and [A-D]"
  - "N/A"
  - "Unknown"
```

### Domain-Specific Rules

```yaml
domains:
  cybersecurity:
    prohibited_patterns:
      - "according to (?:NIST|OWASP|ISO|CIS)"
      - "per (?:NIST|OWASP|ISO) (?:SP|publication)"
    parenthetical_patterns:
      - "\\((?:deprecated|obsolete|outdated)[^)]*\\)"
      - "\\((?:wrong|incorrect|insecure)[^)]*\\)"
```

## near_peer_matrix.yaml

Confusion matrices for near-peer distractors:

```yaml
confusion_matrices:
  cryptography:
    - [Encryption, Hashing, Encoding, Obfuscation]
    - [Symmetric Key, Asymmetric Key, Session Key, Master Key]
    - [AES, DES, 3DES, Blowfish]
    - [SHA-256, MD5, SHA-1, RIPEMD]

  authentication:
    - [Authentication, Authorization, Accounting, Auditing]
    - [MFA, 2FA, SFA, Passwordless]
    - [OAuth, OIDC, SAML, Kerberos]

  network_security:
    - [Firewall, IDS, IPS, WAF]
    - [VPN, Proxy, Tor, SSH Tunnel]
    - [TLS, SSL, HTTPS, IPSec]
```

**Usage**: For definition questions, distractors MUST come from the same row as the correct answer.

## voter_presets.yaml

System prompts for each voter agent:

```yaml
voters:
  pedagogy:
    system_prompt: |
      You are an expert in educational pedagogy, specializing in
      Bloom's Taxonomy and cognitive learning levels...
    evaluation_criteria:
      - cognitive_level_alignment
      - learning_objective_clarity
      - knowledge_depth

  active_learning:
    system_prompt: |
      You are an expert in active learning strategies...
    evaluation_criteria:
      - student_engagement
      - discussion_prompts
      - problem_solving_integration
```

## Environment Variables

Required environment variables:

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key for LLM access |
| `EXA_API_KEY` | No | Exa API key for semantic search |

Set in `.env` file:

```bash
OPENROUTER_API_KEY=sk-or-...
EXA_API_KEY=...
```

## Runtime Configuration

### Batch Processing

Override via CLI:

```bash
--batch-size 100        # Topics per batch
--batch-delay 2.0       # Seconds between batches
--concurrent 100        # Max concurrent requests
```

### Flashcard Generation

```bash
--num-flashcards 15     # Cards per topic
--search-context-size medium  # low/medium/high
```

### Token Limits

The `max_tokens: 50000` setting for flashcard generation allows:
- Batch processing of multiple flashcards per request
- Detailed distractor reasoning
- Full citation inclusion
- Comprehensive metadata

## Production Run Metrics

From the 2026-01-01 validation run:

| Metric | Value |
|--------|-------|
| Total topics processed | 11,786 |
| Total flashcards generated | 180,753 |
| Total duration | 6.8 hours |
| Average flashcards per topic | 15.3 |
| Throughput | ~29 topics/minute |

## See Also

- [Architecture](architecture.md)
- [CLI Reference](cli-reference.md)
- [Config Directory README](../config/README.md)
