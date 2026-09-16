# Structuring LLM Tool Calls with Pydantic and JSON Serialization

## 1. Why Structured Tool Calls Matter
While LLMs can freely generate text, binding tools allows you to extract parameters and call external functions. A schema or data model is a code-level extension of this capability that enforces specific output formats (such as Python classes, dictionaries, or JSON). 

This guarantees that outputs integrating with APIs, databases, or downstream functions are structured, predictable, and reliable.

### Conceptual Examples
* **Weather API Schema:** Expects weather conditions (`str`), temperature (`int`), and unit (`str`).
  ```python
  from pydantic import BaseModel, Field

  class WeatherSchema(BaseModel):
      condition: str = Field(description="Weather condition such as sunny, rainy, cloudy")
      temperature: int = Field(description="Temperature value")
      unit: str = Field(description="Temperature unit such as fahrenheit or celsius")
