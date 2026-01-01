# Extending Voters Guide

This guide shows how to create custom voter agents for specialized evaluation.

## Voter Architecture

### How Voters Work

1. Each voter receives topic data and optional research context
2. Voter evaluates against specific criteria
3. Returns a `VoterBallot` with decision, confidence, and suggestions
4. Orchestrator aggregates all ballots for consensus

### Ballot Structure

```python
@dataclass
class VoterBallot:
    voter_name: str
    decision: str  # APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, REJECT
    confidence: float  # 0.0 - 1.0
    scores: Dict[str, float]
    suggestions: List[Suggestion]
    reasoning: str
    weight: float = 1.0
```

## Creating a Custom Voter

### Step 1: Create Voter Class

```python
# prompt_optimizer/voters/security_voter.py

from typing import Optional
from prompt_optimizer.voters.base import BaseVoter
from prompt_optimizer.models import (
    TopicData,
    VoterBallot,
    Suggestion,
    ExaResearchContext,
)


class SecurityVoter(BaseVoter):
    """
    Evaluates security topics for accuracy and best practices.

    Checks:
    - Security terminology accuracy
    - Best practice alignment
    - Vulnerability coverage
    - Mitigation completeness
    """

    def __init__(self, openrouter_client, weight: float = 1.0):
        super().__init__(
            name="SecurityVoter",
            openrouter_client=openrouter_client,
            weight=weight,
            max_tokens=2000,
        )

        self.system_prompt = """
        You are a cybersecurity expert evaluating educational content.

        Evaluate the topic for:
        1. Security terminology accuracy
        2. Alignment with industry best practices (NIST, OWASP, CIS)
        3. Coverage of relevant vulnerabilities
        4. Completeness of mitigation strategies

        Provide:
        - Decision: APPROVE, APPROVE_WITH_SUGGESTIONS, REVISE, or REJECT
        - Confidence: 0.0 to 1.0
        - Specific suggestions for improvement
        - Reasoning for your decision
        """

    async def evaluate(
        self,
        topic_data: TopicData,
        research_context: Optional[ExaResearchContext] = None,
    ) -> VoterBallot:
        """Evaluate topic for security accuracy."""

        # Build evaluation prompt
        prompt = self._build_prompt(topic_data, research_context)

        # Get LLM response
        response = await self.openrouter.chat_completion(
            messages=[
                {"role": "system", "content": self.system_prompt},
                {"role": "user", "content": prompt},
            ],
            max_tokens=self.max_tokens,
        )

        # Parse response into ballot
        return self._parse_response(response)

    def _build_prompt(
        self,
        topic_data: TopicData,
        research_context: Optional[ExaResearchContext],
    ) -> str:
        """Build evaluation prompt."""

        prompt = f"""
        Evaluate this security topic:

        Topic: {topic_data.topic_title}

        Hierarchy:
        - Category: {topic_data.hierarchy.level_1_category}
        - Domain: {topic_data.hierarchy.level_2_domain}
        - Subdomain: {topic_data.hierarchy.level_3_subdomain}
        - Topic: {topic_data.hierarchy.level_6_topic}
        """

        if research_context:
            prompt += f"""

        Research Context:
        {research_context.summary}

        Sources:
        {chr(10).join(f'- {s.title}: {s.url}' for s in research_context.sources[:3])}
        """

        prompt += """

        Respond in JSON format:
        {
            "decision": "APPROVE|APPROVE_WITH_SUGGESTIONS|REVISE|REJECT",
            "confidence": 0.0-1.0,
            "scores": {
                "accuracy": 0.0-1.0,
                "best_practices": 0.0-1.0,
                "vulnerability_coverage": 0.0-1.0,
                "mitigation_completeness": 0.0-1.0
            },
            "suggestions": [
                {
                    "content": "suggestion text",
                    "priority": "HIGH|MEDIUM|LOW",
                    "category": "security"
                }
            ],
            "reasoning": "explanation"
        }
        """

        return prompt

    def _parse_response(self, response: str) -> VoterBallot:
        """Parse LLM response into ballot."""
        import json

        try:
            data = json.loads(response)

            suggestions = [
                Suggestion(
                    content=s["content"],
                    priority=s.get("priority", "MEDIUM"),
                    category=s.get("category", "security"),
                )
                for s in data.get("suggestions", [])
            ]

            return VoterBallot(
                voter_name=self.name,
                decision=data["decision"],
                confidence=data["confidence"],
                scores=data.get("scores", {}),
                suggestions=suggestions,
                reasoning=data.get("reasoning", ""),
                weight=self.weight,
            )

        except (json.JSONDecodeError, KeyError) as e:
            # Fallback for parsing errors
            return VoterBallot(
                voter_name=self.name,
                decision="REVISE",
                confidence=0.5,
                scores={},
                suggestions=[],
                reasoning=f"Parse error: {e}",
                weight=self.weight,
            )
```

### Step 2: Register the Voter

Option A: Add to existing voters:

```python
# prompt_optimizer/voters/__init__.py

from .security_voter import SecurityVoter

def get_all_voters(openrouter_client, exa_client=None):
    """Get all voter instances."""
    voters = [
        PedagogyVoter(openrouter_client),
        ActiveLearningVoter(openrouter_client),
        # ... existing voters ...
        SecurityVoter(openrouter_client),  # Add new voter
    ]
    return voters
```

Option B: Create custom voter set:

```python
# my_custom_voters.py

from prompt_optimizer.voters import get_all_voters
from prompt_optimizer.voters.security_voter import SecurityVoter

def get_custom_voters(openrouter_client, exa_client=None):
    """Get custom voter set."""
    base_voters = get_all_voters(openrouter_client, exa_client)
    custom_voters = [
        SecurityVoter(openrouter_client, weight=1.2),
    ]
    return base_voters + custom_voters
```

### Step 3: Use Custom Voters

```python
import asyncio
from prompt_optimizer.services import OpenRouterClient
from my_custom_voters import get_custom_voters

async def main():
    client = OpenRouterClient()
    voters = get_custom_voters(client)

    # Run all voters
    topic = ...
    ballots = await asyncio.gather(*[
        voter.evaluate(topic)
        for voter in voters
    ])

    print(f"Total voters: {len(ballots)}")
    for ballot in ballots:
        print(f"  {ballot.voter_name}: {ballot.decision}")

asyncio.run(main())
```

## Voter Best Practices

### 1. Clear Evaluation Criteria

Define specific, measurable criteria:

```python
self.criteria = [
    "Security terminology accuracy",
    "Best practice alignment",
    "Vulnerability coverage",
    "Mitigation completeness",
]
```

### 2. Structured Prompts

Use clear prompt structure:

```python
prompt = f"""
Evaluate this topic against the following criteria:

1. [Criterion 1]: [Description]
2. [Criterion 2]: [Description]

Topic: {topic_data.topic_title}

Respond with:
- Decision
- Confidence
- Scores for each criterion
- Suggestions for improvement
"""
```

### 3. Robust Parsing

Handle parsing errors gracefully:

```python
try:
    data = json.loads(response)
    return self._create_ballot(data)
except json.JSONDecodeError:
    return self._fallback_ballot()
```

### 4. Appropriate Weight

Set weight based on importance:

```python
# Higher weight for critical evaluation
SecurityVoter(client, weight=1.2)

# Lower weight for optional checks
StyleVoter(client, weight=0.8)
```

### 5. Use Research Context

Incorporate research when available:

```python
if research_context:
    prompt += f"\nResearch: {research_context.summary}"
```

## Testing Custom Voters

```python
import asyncio
import pytest
from prompt_optimizer.services import OpenRouterClient
from prompt_optimizer.models import TopicData, TopicHierarchy
from my_custom_voters import SecurityVoter


@pytest.mark.asyncio
async def test_security_voter():
    client = OpenRouterClient()
    voter = SecurityVoter(client)

    topic = TopicData(
        topic_title="SQL Injection Prevention",
        hierarchy=TopicHierarchy(
            level_1_category="Cybersecurity",
            level_2_domain="Application Security",
            level_3_subdomain="Web Security",
            level_4_entry_domain="Injection Attacks",
            level_5_entry_subdomain="SQL Injection",
            level_6_topic="Prevention Techniques",
        ),
    )

    ballot = await voter.evaluate(topic)

    assert ballot.voter_name == "SecurityVoter"
    assert ballot.decision in [
        "APPROVE",
        "APPROVE_WITH_SUGGESTIONS",
        "REVISE",
        "REJECT",
    ]
    assert 0.0 <= ballot.confidence <= 1.0
```

## See Also

- [Voters API](../api/voters.md)
- [Architecture](../architecture.md)
- [Services API](../api/services.md)
