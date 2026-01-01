# Voters API

The `voters/` module provides 8 specialized evaluation agents for prompt assessment.

## Overview

```python
from prompt_optimizer.voters import get_all_voters

# Initialize all voters
voters = get_all_voters(openrouter_client, exa_client)

# Run voting in parallel
results = await asyncio.gather(*[
    voter.evaluate(topic_data, research_context)
    for voter in voters
])
```

## Voter Architecture

### Consensus Model

- **8 Voters** evaluate prompts from different perspectives
- **Parallel Execution** - all voters run simultaneously
- **Weighted Voting** - pedagogy voter has higher weight (1.2)
- **Consensus Threshold** - 57% (5 of 8 voters must approve)

### Voting Decisions

| Decision | Meaning |
|----------|---------|
| `APPROVE` | Prompt meets all criteria |
| `APPROVE_WITH_SUGGESTIONS` | Acceptable with improvements |
| `REVISE` | Significant changes needed |
| `REJECT` | Fails to meet requirements |

## BaseVoter

Abstract base class for all voters.

### Methods

#### evaluate

```python
async def evaluate(
    topic_data: TopicData,
    research_context: Optional[ExaResearchContext] = None,
) -> VoterBallot:
    """Evaluate a topic and return ballot."""
```

### VoterBallot Structure

```python
@dataclass
class VoterBallot:
    voter_name: str
    decision: str  # APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, REJECT
    confidence: float  # 0.0 - 1.0
    scores: Dict[str, float]  # overall, clarity, pedagogy, accuracy, completeness
    suggestions: List[Suggestion]
    reasoning: str
```

## Voter Implementations

### PedagogyVoter

Evaluates Bloom's Taxonomy alignment and cognitive levels.

```python
from prompt_optimizer.voters import PedagogyVoter

voter = PedagogyVoter(openrouter_client)
ballot = await voter.evaluate(topic_data, research_context)
```

**Evaluation Criteria:**
- Cognitive level alignment
- Learning objective clarity
- Knowledge depth progression
- Bloom's Taxonomy coverage

**Weight**: 1.2 (higher influence)

### ActiveLearningVoter

Evaluates student engagement and interaction.

**Evaluation Criteria:**
- Discussion prompt quality
- Problem-solving integration
- Student participation opportunities
- Interactive element design

### ScaffoldingVoter

Evaluates learning progression and support structures.

**Evaluation Criteria:**
- Progressive complexity
- Prerequisite sequencing
- Support scaffolds
- Gradual release of responsibility

### ConnectionVoter

Evaluates interdisciplinary links and prerequisites.

**Evaluation Criteria:**
- Prerequisite mapping accuracy
- Cross-domain connections
- Knowledge integration points
- Conceptual bridging

### AssessmentVoter

Evaluates formative assessment strategies.

**Evaluation Criteria:**
- Checkpoint design
- Feedback opportunities
- Mastery indicators
- Assessment variety

### ClarityVoter

Evaluates language accessibility and structure.

**Evaluation Criteria:**
- Technical terminology clarity
- Sentence structure
- Content organization
- Jargon explanation

### SchemaVoter

Validates JSON/XML structure and completeness.

**Evaluation Criteria:**
- Required field presence
- Format compliance
- Structural integrity
- Schema validation

### ExaGroundingVoter

Verifies facts against web sources.

**Evaluation Criteria:**
- Source accuracy
- Fact verification
- Citation quality
- Information currency

## Example Usage

### Single Voter

```python
import asyncio
from prompt_optimizer.services import OpenRouterClient
from prompt_optimizer.voters import PedagogyVoter
from prompt_optimizer.models import TopicData

async def main():
    client = OpenRouterClient()
    voter = PedagogyVoter(client)

    topic = TopicData(
        topic_title="TLS Handshake",
        hierarchy=...,
    )

    ballot = await voter.evaluate(topic)

    print(f"Decision: {ballot.decision}")
    print(f"Confidence: {ballot.confidence:.2f}")
    print(f"Suggestions: {len(ballot.suggestions)}")

asyncio.run(main())
```

### All Voters (Parallel)

```python
import asyncio
from prompt_optimizer.services import OpenRouterClient, ExaClient
from prompt_optimizer.voters import get_all_voters
from prompt_optimizer.models import TopicData

async def main():
    openrouter = OpenRouterClient()
    exa = ExaClient()

    voters = get_all_voters(openrouter, exa)

    topic = TopicData(
        topic_title="TLS Handshake",
        hierarchy=...,
    )

    # Run all voters in parallel
    ballots = await asyncio.gather(*[
        voter.evaluate(topic)
        for voter in voters
    ])

    # Count approvals
    approvals = sum(1 for b in ballots if "APPROVE" in b.decision)
    print(f"Approvals: {approvals}/8 ({approvals/8:.0%})")

asyncio.run(main())
```

## Extending Voters

### Creating a Custom Voter

```python
from prompt_optimizer.voters.base import BaseVoter
from prompt_optimizer.models import VoterBallot, TopicData

class CustomVoter(BaseVoter):
    """Custom voter for specific evaluation."""

    def __init__(self, openrouter_client):
        super().__init__(
            name="CustomVoter",
            openrouter_client=openrouter_client,
            weight=1.0,
        )

    async def evaluate(
        self,
        topic_data: TopicData,
        research_context=None,
    ) -> VoterBallot:
        # Build evaluation prompt
        prompt = self._build_evaluation_prompt(topic_data)

        # Get LLM response
        response = await self.openrouter.chat_completion(
            messages=[{"role": "user", "content": prompt}],
            max_tokens=self.max_tokens,
        )

        # Parse response into ballot
        return self._parse_response(response)

    def _build_evaluation_prompt(self, topic_data):
        return f"""
        Evaluate the following topic for [your criteria]:

        Topic: {topic_data.topic_title}
        Hierarchy: {topic_data.hierarchy}

        Provide evaluation with:
        - Decision: APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, or REJECT
        - Confidence: 0.0 - 1.0
        - Suggestions for improvement
        """
```

### Registering Custom Voter

```python
from prompt_optimizer.voters import get_all_voters

def get_custom_voters(openrouter, exa):
    voters = get_all_voters(openrouter, exa)
    voters.append(CustomVoter(openrouter))
    return voters
```

## See Also

- [Services API](services.md)
- [Models API](models.md)
- [Extending Voters Guide](../guides/extending-voters.md)
