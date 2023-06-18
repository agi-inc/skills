# 🪄 AI Skills Hub

AI Skills Hub is an open-source library aiming to simplify the integration of AI Skills into MULTION AI.

It provides utility functions to get a list of active skills from [agihub.io](https://agihub.io/) directory, get skill manifests, and extract OpenAPI specifications and load skills.

## Installation

You can install it using pip:

```python
pip install multion-skills
```

## Quick Start Example

**Load and Call Step by Step:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/plugins_step_by_step.ipynb)


## More Examples

**Generate Prompt with Plugins Description:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/create_prompt_plugins.ipynb)

**Plugins Retrieval:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/plugin_retriever_with_langchain_agent.ipynb)


## Usage

### Get a list of plugins

- `urls = get_plugins()`: Get a list of available plugins from a [plugins repository](https://www.plugplai.com/).

- `urls = get_plugins(filter = 'ChatGPT', category='dev')`: Use 'filter' or 'category' variables to query specific plugins

Example:

```python
import multion as ai

# Get all plugins from agihub.io
urls = ai.get_skills()

#  Get ChatGPT plugins - only ChatGPT verified plugins
urls = ai.get_skills(filter = 'ChatGPT')

#  Get working plugins - only tested plugins (in progress)
urls = ai.get_skills(filter = 'working')

#  Get plugins by category - only tested plugins (in progress)
urls = ai.get_skills(category = 'travel')

#  Get the names list of categories
urls = ai.get_category_names()
```

### Utility Functions

Help to load the plugins manifest and OpenAPI specification

- `manifest = get_skills_manifest(url)`: Get the AI Skill manifest from the specified skill URL.
- `specUrl = get_openapi_url(url, manifest)`: Get the OpenAPI URL from the skill manifest.
- `spec = get_openapi_spec(openapi_url)`: Get the OpenAPI specification from the specified OpenAPI URL.
- `manifest, spec = spec_from_url(url)`: Returns the Manifest and OpenAPI specification from the skill URL.

Example:

```python
import multion as ai

# Get the Manifest and the OpenAPI specification from the plugin URL
manifest, openapi_spec = ai.spec_from_url(urls[0])
```


### Load Skills
**Example:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/plugins_step_by_step.ipynb)

```python
from multion import Skills


# Initialize 'Skills' by passing a list of urls, this function will
# load the skills and build a default description to be used as prefix prompt
skilla = Skills.install_and_activate(urls)

#  Print the deafult prompt for the activated plugins
print(skills.prompt)

#  Print the number of tokens of the prefix prompt
print(skills.tokens)
```

Example on installing (loading) all plugins, and activating a few later:

```python
from multion import Skills

# If you just want to load the plugins, but activate only
# some of them later use Skills(urls) instead
skills = Skills(urls)

# Print the names of installed plugins
print(skills.list_installed)

# Activate the plugins you want
skills.activate(name1)
skills.activate(name2)
skills.activate(name3)

# Deactivate the last plugin
skills.deactivate(name3)
```

### Prompt and Tokens Counting

The `skills.prompt` attribute contains a prompt with descriptions of the active plugins.
The `skills.tokens` attribute contains the number of tokens in the prompt.

For example:
```python
skills = Skills.install_and_activate(urls)
print(skills.prompt)
print(skills.tokens)
```
This will print the prompt with skills descriptions and the number of tokens.


### Parse LLM Response for API Tag

The `parse_llm_response()` function parses an LLM response searching for API calls. It looks for the `<API>` pattern defined in the `skills.prompt` and extracts the skills name, operation ID, and parameters.


### Call API

The `call_api()` function calls an operation in an active skill. It takes the skills name, operation ID, and parameters extracted by `parse_llm_response()` and makes a request to the skills API.


### Apply Plugins

The `@skills.apply_skills` decorator can be used to easily apply active plugins to an LLM function. To use it:

1. Import the Plugins class and decorator:

```python
from multion import Skills, skills.apply_skills
```

2. Define your LLM function, that necessarily takes a string (the user input) as the first argument and returns a string (the response):

```python
@plugins.apply_skills
def call_llm(user_input):
  ...
  return response
```

3. The decorator will handle the following:

- Prepending the prompt (with plugin descriptions) to the user input 
- Checking the LLM response for API calls (the <API>...</API> pattern)
- Calling the specified skills 
- Summarizing the API calls in the LLM response
- Calling the LLM function again with the summary to get a final response

4. If no API calls are detected, the original LLM response is returned.

To more details on the implementation of these steps, see example "Step by Step": [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/plugins_step_by_step.ipynb)

### Skills Retrieval
**Example:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/edreisMD/plugnplai/blob/main/examples/plugin_retriever_with_langchain_agent.ipynb)


```python
from multion import SkillRetriever

# Initialize the skill retriever vector database and index the skill descriptions.
# Loading the skills from agihub.io directory
skill_retriever = SkillRetriever.from_directory()

#  Retrieve the names of the skills given a user's message
skill_retriever.retrieve_names("what shirts can i buy?")
```


## Contributing

AI Skills is an open source library, and we welcome contributions from the entire community. If you're interested in contributing to the project, please feel free to fork, submit pull requests, report issues, or suggest new features.

#### To dos
- [x] [Load] Define a default object to read skills - use LangChain standard? (for now using only manifest and specs jsons)  
- [ ] [Load] Fix breaking on reading certain skills specs  
- [x] [Load] Accept different specs methods and versions (param, query, body)  
- [x] [Prompt] Build a utility function to return a default prompts for a skill  
- [x] [Prompt] Fix prompt building for body (e.g. "Speak")   
- [x] [Prompt] Build a utility function to return a default prompts for a set of skills  
- [x] [Prompt] Build a utility function to count tokens of the skills prompt  
- [ ] [Prompt] Use the prompt tokens number to short or expand a skills prompt, use LLM to summarize the prefix prompt  
- [x] [CallAPI] Build a function to call API given a dictionary of parameters  
- [x] [CallAPI] Add example for calling API  
- [ ] [Embeddings] Add filter option (e.g. "working", "ChatGPT") to "SkillRetriever.from_directory()"  
- [ ] [Docs] Add Sphynx docs  
- [ ] [Verification] Build automated tests to verify new skills  
- [ ] [Verification] Build automated monitoring for working skills  
- [ ] [Website] Build an open-source website  

#### Project Roadmap
1. Build auxiliary functions that helps everyone to use plugins as defined by [OpenAI](https://platform.openai.com/docs/plugins/introduction)  
2. Build in compatibility with different open-source formats (e.g. LangChain, BabyAGI, etc)  
3. Find a best prompt format for skills, optimizing for token number and description completness  
4. Build a dataset to finetune open-source models to call skills  
5. Finetune an open-source model to call skills  
6. Help with authentication  
7. Etc.  

## Links
- Skills directory: [https://agihub.io/](https://agithub.io/)  
- API reference: [https://agihub.github.io/](https://agithub.github.io/)
