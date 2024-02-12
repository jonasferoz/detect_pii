## Details

| Developed by | Guardrails AI |
| --- | --- |
| Date of development | Feb 15, 2024 |
| Validator type | Privacy, Security |
| Blog |  |
| License | Apache 2 |
| Input/Output | Input, Output |

## Description

This validator ensures that any given text does not contain PII. This validator uses Microsoft's Presidio (https://github.com/microsoft/presidio) to detect PII in the text. If PII is detected, the validator will fail with a programmatic fix that anonymizes the text. Otherwise, the validator will pass.

### Resources required

- Dependencies: `presidio_analyzer`, `presidio_anonymizer`

## Installation

```bash
$ gudardrails hub install hub://guardrails/detect_pii
```

## Usage Examples

### Validating string output via Python

```python
# Import Guard and Validator
from guardrails.hub import DetectPII
from guardrails import Guard

# Initialize Validator
val = DetectPII(
    pii_entities=["EMAIL_ADDRESS", "PHONE_NUMBER"],
    on_fail="fix"
)

# Setup Guard
guard = Guard.from_string(
    validators=[val, ...],
)

guard.parse("Good morning!")  # Validator passes
guard.parse("If interested, apply at not_a_real_email@guardrailsai.com")  # Validator fails
```

## Validating JSON output via Python

In this example, we apply the validator to a string field of a JSON output generated by an LLM.

```python
# Import Guard and Validator
from pydantic import BaseModel
from guardrails.hub import ValidChoices
from guardrails import Guard

# Initialize Validator
val = DetectPII(
    pii_entities=["EMAIL_ADDRESS", "PHONE_NUMBER"],
    on_fail="fix"
)

# Create Pydantic BaseModel
class UserHistory(BaseModel):
    name: str
    last_msg: str = Field(
        description="Last message sent by user", validators=[val]
    )

# Create a Guard to check for valid Pydantic output
guard = Guard.from_pydantic(output_class=UserHistory)

# Run LLM output generating JSON through guard
guard.parse("""
{
    "name": "John Smith",
    "last_msg": "My account isn't working. My username is not_a_real_email@guardrailsai.com"
}
""")
```

## API Reference

- `pii_entities`: The types of PII entities to filter out. For a full list of entities look at https://microsoft.github.io/presidio/
 - `on_fail`: The policy to enact when a validator fails.
