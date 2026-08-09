# In-Context Learning
* Method of prompt engineering
* Demonstrations of the task provided to the LLM as part of the prompt
* Doesn't require additional training
* New task learned from a small set of examples presented within the context at inference time
## Advantages:
* No fine-tuning needed
* Reduce time and resource consumption
* Improves performance

## Disadvantages:
* Limited to what can fit in-context
* Complex tasks need gradient steps
* Involves adjustments based on error gradients

# Intro Prompt engineering
* Prompts: Instructions or inputs given to an LLM designed to guide it toward performing a specific task or generating a desired output.
## 2 main components to a prompt:
* Instructions ( Need to be clear, direct commands that tell the AI what to do & need to be specific to ensure LLM understands the task )
* Context ( Information that helps LLM make sense of instructions. ex: data, parameters or any relevant details that shape that AI's response )
By combining these element effectively, you can tailor LLMs effectively.
## What is prompt engineering?
* Designing and refining prompts to communicate with AI systems (LLMs)
* Involves crafting questions, commands, or statements to elicit accurate, relevant, and contextually appropriate responses.
* Fundamental to customer service and computational linguistics
## Why prompt engineering?
* Directly influences the effectiveness and accuracy of LLMs
* Ensures LLMs generate relevant, precise, and contextually appropriate responses.
* Meets user needs through clearer prompts and reduced misunderstanding
* Eliminates the need for continual fine-tuning
## Elements of a prompt
* Instructions
* Context
* Input data ( actual data the LLM will process )
* Output indicator ( Part of the prompt where the LLM's response is expected )
