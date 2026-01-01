# Voters Directory

8 specialized voter agents for prompt evaluation.

## Overview

The voting system uses a consensus model where 8 specialized agents evaluate prompts from different perspectives.

## Voter Agents

| Voter | Purpose | Weight |
|-------|---------|--------|
| `PedagogyVoter` | Bloom's Taxonomy, cognitive levels | 1.2 |
| `ActiveLearningVoter` | Student engagement, discussion | 1.0 |
| `ScaffoldingVoter` | Progressive complexity | 1.0 |
| `ConnectionVoter` | Interdisciplinary links | 1.0 |
| `AssessmentVoter` | Formative assessment strategies | 1.0 |
| `ClarityVoter` | Language accessibility | 1.0 |
| `SchemaVoter` | JSON structure validation | 1.0 |
| `ExaGroundingVoter` | Fact verification, sources | 1.0 |

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Voter factory and exports |
| `base.py` | BaseVoter abstract class |
| `pedagogy_voter.py` | Pedagogy evaluation |
| `active_learning_voter.py` | Engagement evaluation |
| `scaffolding_voter.py` | Progression evaluation |
| `connection_voter.py` | Links evaluation |
| `assessment_voter.py` | Assessment evaluation |
| `clarity_voter.py` | Clarity evaluation |
| `schema_voter.py` | Structure validation |
| `exa_grounding_voter.py` | Fact verification |

## Usage

```python
from prompt_optimizer.voters import get_all_voters

# Initialize all voters
voters = get_all_voters(openrouter_client, exa_client)

# Run voting in parallel
import asyncio
ballots = await asyncio.gather(*[
    voter.evaluate(topic_data, research_context)
    for voter in voters
])

# Check consensus
approvals = sum(1 for b in ballots if "APPROVE" in b.decision)
consensus = approvals >= 5  # 57% threshold
```

## Voting Decisions

| Decision | Meaning |
|----------|---------|
| `APPROVE` | Meets all criteria |
| `APPROVE_WITH_SUGGESTIONS` | Acceptable with improvements |
| `REVISE` | Significant changes needed |
| `REJECT` | Fails requirements |

## Consensus Threshold

- **5 of 8 voters** must approve (57%)
- Weighted voting gives pedagogy more influence
- Suggestions are clustered using Jaccard similarity

## Extending Voters

See [Extending Voters Guide](../docs/guides/extending-voters.md) for adding custom voters.

## See Also

- [Voters API](../docs/api/voters.md)
- [Architecture](../docs/architecture.md)
