# Overview

| Developed by | Guardrails AI |
| Date of development | Feb 15, 2024 |
| Validator type | Safety |
| Blog |  |
| License | Apache 2 |
| Input/Output | Input, Output |

## Description

### Intended Use
This validator monitors any text (input or output) and detects secrets present in the text. Under-the-hood, the validator uses the `detect-secrets` library to check whether the text contains any secrets. If any secrets are detected, the validator fails and returns the text with the secrets replaced with asterisks. Otherwise, the validator returns the generated code snippet.

### Requirements

* Dependencies: 
    - guardrails-ai>=0.4.0
    - detect-secrets

## Installation

```bash
$ guardrails hub install hub://guardrails/secrets_present
```

## Usage Examples

### Validating string output via Python

In this example, we apply the validator to a string output generated by an LLM.

```python
# Import Guard and Validator
from guardrails import Guard
from guardrails.hub import SecretsPresent

# Setup Guard
guard = Guard().use(
    SecretsPresent, on_fail="exception"
)

response = guard.validate(
    """
    def hello():
        name = "James"
        age = 25
        return {"name": name, "age": age}
    """
)  # Validator passes

try:
    response = guard.validate(
        """
        def hello():
            user_id = "1234"
            user_pwd = "password1234"
            user_api_key = "sk-xhdfgtest"
        """
    )  # Validator fails
except Exception as e:
    print(e)
```
Output:
```console
Validation failed for field with errors: The following secrets were detected in your response:
password1234
sk-xhdfgtest
```

# API Reference

**`__init__(self, on_fail="noop")`**
<ul>
Initializes a new instance of the Validator class.

**Parameters**
- **`on_fail`** *(str, Callable)*: The policy to enact when a validator fails. If `str`, must be one of `reask`, `fix`, `filter`, `refrain`, `noop`, `exception` or `fix_reask`. Otherwise, must be a function that is called when the validator fails.
</ul>
<br/>

**`__call__(self, value, metadata={}) -> ValidationOutcome`**
<ul>
Validates the given `value` using the rules defined in this validator, relying on the `metadata` provided to customize the validation process. This method is automatically invoked by `guard.parse(...)`, ensuring the validation logic is applied to the input data.

Note:

1. This method should not be called directly by the user. Instead, invoke `guard.parse(...)` where this method will be called internally for each associated Validator.
2. When invoking `guard.parse(...)`, ensure to pass the appropriate `metadata` dictionary that includes keys and values required by this validator. If `guard` is associated with multiple validators, combine all necessary metadata into a single dictionary.

**Parameters**
- **`value`** *(Any)*: The input value to validate.
- **`metadata`** *(dict)*: A dictionary containing metadata required for validation. No additional metadata keys are needed for this validator.

</ul>
  
