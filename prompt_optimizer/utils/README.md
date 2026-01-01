# Utils Directory

Helper utilities for formatting and validation.

## Overview

Provides formatting utilities for MathJax, code blocks, and quality validation.

## Components

| File | Purpose |
|------|---------|
| `__init__.py` | Module exports |
| `mathjax_formatter.py` | LaTeX math rendering for Anki |
| `code_formatter.py` | Markdown to HTML code conversion |
| `quality_validation.py` | Distractor quality checks |
| `question_validator.py` | 7-step synthesis validation |

## MathJax Formatter

Converts LaTeX delimiters for Anki compatibility:

```python
from prompt_optimizer.utils import (
    convert_delimiters,
    process_question,
    has_math,
)

# Convert $...$ to \(...\)
text = convert_delimiters("The formula $E=mc^2$ is famous")
# Result: "The formula \(E=mc^2\) is famous"

# Check if text contains math
if has_math(text):
    text = process_question(text)
```

### Supported Conversions

| Input | Output |
|-------|--------|
| `$...$` | `\(...\)` |
| `$$...$$` | `\[...\]` |
| `\(...\)` | (unchanged) |
| `\[...\]` | (unchanged) |

## Code Formatter

Converts Markdown code blocks to HTML for Anki:

```python
from prompt_optimizer.utils import (
    convert_markdown_code_to_html,
    process_question_code,
)

# Convert code block
text = """
```python
def hello():
    print("Hello")
```
"""

html = convert_markdown_code_to_html(text)
# Result: <pre><code class="language-python">...</code></pre>
```

### CSS for Anki

```python
from prompt_optimizer.utils import get_anki_code_css

css = get_anki_code_css()
# Include in Anki card template
```

## Quality Validation

Phase 15 distractor quality checks:

```python
from prompt_optimizer.utils import (
    check_distractor_distinctness,
    check_distractor_length_consistency,
    check_blocked_patterns,
    evaluate_distractor_quality,
)

# Check edit distance
result = check_distractor_distinctness(
    correct_answer="TLS 1.3",
    distractors=["TLS 1.2", "SSL 3.0", "HTTPS"],
    min_edit_distance=3,
)

# Check length consistency
result = check_distractor_length_consistency(
    correct_answer="TLS 1.3",
    distractors=["TLS", "Transport Layer Security version 1.3"],
    max_ratio=3.0,
)
```

## Question Validator

7-step synthesis validation:

```python
from prompt_optimizer.utils import (
    QuestionValidator,
    validate_flashcard,
)

validator = QuestionValidator()
result = validator.validate(flashcard)

if not result.passed:
    print(f"Failed: {result.errors}")
```

### Validation Steps

1. **Parenthetical Giveaway Check** - AUTOMATIC FAIL
2. **Schema Compliance** - Required fields
3. **Domain Validity** - Domain-appropriate content
4. **Misconception Differentiation** - Unique misconceptions
5. **Plausibility Verification** - Substantive explanations
6. **Originality Check** - No prohibited phrases
7. **Pattern Diversity** - Varied question structures

## See Also

- [Configuration](../docs/configuration.md)
- [Flashcard Generator](../services/README.md)
