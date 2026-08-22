# LangChain Chains and Agents for building apps
Chains: sequence of calls, whether to an LLM, a tool, or a data preprocessing step.

Input -> Chain 1 -> Output1 -> Chain 2 -> Output2

Example: Aim of the next chains is to identify the recipe and the estimated cooking time for the famous dish avialable in the inserted location.
* Leverage Chain 1 for selecting geographic region to get the famous dish in that location. Uses user's prompt as input for a specific dish based on the user's location. Output will be the dish itself. China -> Pecking duck
* Chain 2 for providing the recipe. Input: Pecking duck, Output: recipe itself.
* Chain 3 for estimating the cooking time. Input : Recipe, Output: Cooking time taken for it.

## code example of chain 3:
template = """Given the recipe {recipe}, estimate how much time I need to cook. it.

YOUR RESPONE:

"""

prompt_template = PromptTemplate(template = template, input_variables = ["recipe"])
recipe_chain = LLMChain(llm=mixtral_llm, prompt=prompt_template, output_key="time")

##  overall chain code
overall_chain = SequentialChain(chains=[location_chain, dish_chain, recipe_chain], 
input_variables = ['location'],
output_variables = ['meal','recipe','time',
verbose = True)

overall_chain.Invoke(input={'location'='china'})
