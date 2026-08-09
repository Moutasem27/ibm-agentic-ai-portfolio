# Advanced methods
## Zero-shot prompt
Instructs and LLM to perform a task without any prior specific training or examples.
Example:
* Classify the following statement as true or false:
'The Eiffel Tower is located in Berlin.'

Answer: This task requires LLM to understand the context and information without any previous tuning for this specific query.

## One-shot prompt
Gives the LLM a single example to help it perform a similar task.
Example:
* Translating a sentence from English to French:
English: "How is the weather today?"
French: "Comment est le temps aujourd'hui"
Now, translate the following sentence from English to French:
English: "Where is the nearest supermarket?"
French: AI will follow template that u gave above

## Few-shot prompt
Learns from a small set of examples before tackling a similar task to generalize from a few instances to new data
Example:
* Here are a few examples of classifying emotions in statements:
Statement: "I just won my first marathon!"
Emotion: Joy
Statement: "I can't believe I lost my keys again."
Emotion: Frustration
Statement: "My best friend is moving to another country."
Emotion: Sadness
Now, Classify the emotion in the following statement:
Statement: "That movie was so scary I had to cover my eyes."
Emotion: LLM will output the emotion

## Chain-of-thought (CoT) prompting
Guides LLMs through complex reasoning in a step-by-step manner. This method is highly effective where the solution requires multiple intermediate steps or reasoning that mimics human thought processes.
Example:
* Model input: Consider the problem: 'A store had 22 apples. They sold 15 apples today and got a new delivery of 8 apples. How many apples are there now?"
Break down each step of your calculation
* Model output: Sure, let's break down the calculation step by step:
1. Initial number of apples: Starts with the number of apples the store had, which is 22.
2. Apples sold: Subtract the number of apples sold today from the initial number. The store sold 15 apples, so subtract 15 from 22: 22 - 15 = 7.
3. New delivery: Add the number of apples from the new delivery. The store received 8 new apples, so add 8 to the remaining apples: 7 + 8 = 15.
So, after selling 15 apples and receiving a new delivery of 8 apples, the store now has 15 apples.

## Self-consistency
Used to enhance the reliability and accuracy of outputs. It also involves generating multiple independent answers to the same question and then evaluate these answers to determine the most consistent result.
Example:
* Model input: When I was 6, my sister was half of my age. Now I am 79, what age is my sister?
* Model output:
