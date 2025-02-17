# Completion Token Usage & Cost
By default LiteLLM returns token usage in all completion requests ([See here](https://litellm.readthedocs.io/en/latest/output/))

However, we also expose 5 public helper functions to calculate token usage across providers:

- `token_counter`: This returns the number of tokens for a given input - it uses the tokenizer based on the model, and defaults to tiktoken if no model-specific tokenizer is available. 

- `cost_per_token`: This returns the cost (in USD) for prompt (input) and completion (output) tokens. It utilizes our model_cost map which can be found in `__init__.py` and also as a [community resource](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json).

- `completion_cost`: This returns the overall cost (in USD) for a given LLM API Call. It combines `token_counter` and `cost_per_token` to return the cost for that query (counting both cost of input and output). 

- `get_max_tokens`: This returns a dictionary for a specific model, with it's max_tokens, input_cost_per_token and output_cost_per_token

- `model_cost`: This returns a dictionary for all models, with their max_tokens, input_cost_per_token and output_cost_per_token [**List of all models**](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json) (📣 This is a community maintained list. Contributions are welcome! ❤️)

## Example Usage 

### 1. `token_counter`

```python
from litellm import token_counter

messages = [{"user": "role", "content": "Hey, how's it going"}]
print(token_counter(model="gpt-3.5-turbo", messages=messages))
```

### 2. `cost_per_token`

```python
from litellm import cost_per_token

prompt_tokens =  5
completion_tokens = 10
prompt_tokens_cost_usd_dollar, completion_tokens_cost_usd_dollar = cost_per_token(model="gpt-3.5-turbo", prompt_tokens=prompt_tokens, completion_tokens=completion_tokens))

print(prompt_tokens_cost_usd_dollar, completion_tokens_cost_usd_dollar)
```

### 3. `completion_cost`

* Input: Accepts a `litellm.completion()` response
* Output: Returns a `float` of cost for the `completion` call 

```python
from litellm import completion, completion_cost

response = completion(
            model="together_ai/togethercomputer/llama-2-70b-chat",
            messages=messages,
            request_timeout=200,
        )
# pass your response from completion to completion_cost
cost = completion_cost(completion_response=response)
formatted_string = f"${float(cost):.10f}"
print(formatted_string)
```

### 4. `get_max_tokens`

* Input: Accepts a model name - e.g. `gpt-3.5-turbo` (to get a complete list, call `litellm.model_list`)
* Output: Returns a dict object containing the max_tokens, input_cost_per_token, output_cost_per_token

```python 
from litellm import get_max_tokens 

model = "gpt-3.5-turbo"

print(get_max_tokens(model)) # {'max_tokens': 4000, 'input_cost_per_token': 1.5e-06, 'output_cost_per_token': 2e-06}
```

### 5. `model_cost`

* Output: Returns a dict object containing the max_tokens, input_cost_per_token, output_cost_per_token for all models on [community-maintained list](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json)

```python 
from litellm import model_cost 

print(model_cost) # {'gpt-3.5-turbo': {'max_tokens': 4000, 'input_cost_per_token': 1.5e-06, 'output_cost_per_token': 2e-06}, ...}
```

