# Naptha Module Template

This is a base module template for creating agent, tool, agent orchestrator, environment, knowledge base and memory modules. You can check out other examples of modules using the CLI commands with the [Naptha SDK](https://github.com/NapthaAI/naptha-sdk). 

- [Naptha Module Template](#naptha-module-template)
  - [🧩 What are Naptha Modules](#-what-are-naptha-modules)
  - [🏗 Creating a new Naptha Module](#-creating-a-new-naptha-module)
    - [🛠 Prerequisites](#-prerequisites)
      - [Install Poetry](#install-poetry)
    - [🔧 Making Changes to the Module](#-making-changes-to-the-module)
    - [Clone and Install the Module](#clone-and-install-the-module)
      - [Making Changes to the Code](#making-changes-to-the-code)
      - [Making Changes to the Configs](#making-changes-to-the-configs)
  - [🧪 Testing the Module](#-testing-the-module)
    - [🖥️ Test the Module Locally without Node](#️-test-the-module-locally-without-node)
    - [🌐 Test the Module on a Local Node (with a Local Hub)](#-test-the-module-on-a-local-node-with-a-local-hub)
      - [Register the new or updated Module on a local Hub](#register-the-new-or-updated-module-on-a-local-hub)
      - [Running the Module on a local Naptha Node](#running-the-module-on-a-local-naptha-node)
    - [☁️ Test the Module on a hosted Node (with the hosted Naptha Hub)](#️-test-the-module-on-a-hosted-node-with-the-hosted-naptha-hub)
  - [💰 Bounties and Microgrants](#-bounties-and-microgrants)

## 🧩 What are Naptha Modules

Naptha Modules are the building blocks of multi-agent applications, which enable them to run across multiple nodes. There are currently five types of Modules:

- **Agent Modules:** Things like Chat Agents, Task-solving Agents, ReAct Agents, etc.
- **Tool Modules:** Things like Web Search, Python Code Execution, etc.
- **Agent Orchestrator Modules:** Things like Organizations of Coding Agents, Social Simulations, etc.
- **Environment Modules:** Things like Group Chats (like WhatsApp for Agents), Information Board (Reddit for Agents), Auctions (eBay for Agents), etc.
- **Knowledge Base Modules:** Things like Wikipedia, GitHub, etc.
- **Memory Modules:** Things like Chat History, Task History, etc.
- **Persona Modules:** Things like Social Personas generated from exported Twitter data, or synthetically-generated Market Personas

Modules are stored on GitHub, HuggingFace, IPFS, or DockerHub with the URL registered on the Naptha Hub. If you're familiar with Kubeflow Pipelines, Modules are a bit like Components. Modules are based on Poetry Python packages, with some additions like schemas, configs, and an entrypoint. A typical Module has the following structure:

```
- my_module/
  - my_module/
    - __init__.py
    - configs/
      - deployment.json
      - environment_deployments.json
      - llm_configs.json
    - run.py
    - schemas.py
  - tests/
    - __init__.py
  - pyproject.toml
  - poetry.lock
  - README.md
  - LICENSE
  - .env
  - .gitignore
  - Dockerfile
```

You can run Modules locally, or deploy to a Naptha Node using `naptha run` commands from the [Naptha SDK](https://github.com/NapthaAI/naptha-sdk). Modules are executed within Poetry virtual environments or Docker containers on Naptha Nodes.

## 🏗 Creating a new Naptha Module

### 🛠 Prerequisites 

#### Install Poetry 

From the official poetry [docs](https://python-poetry.org/docs/#installing-with-the-official-installer):

```bash
curl -sSL https://install.python-poetry.org | python3 -
export PATH="/home/$(whoami)/.local/bin:$PATH"
```

### 🔧 Making Changes to the Module

Before deploying a new or updated module to a Naptha node, you should iterate on improvements with the module locally. 

### Clone and Install the Module

Clone the repo using:

```bash
git clone https://github.com/NapthaAI/<module_name>
cd <module_name>
```

Create a copy of the .env file:

```bash
cp .env.example .env
```

If your module calls others modules (e.g. using Agent(), Tool(), or Environment()), you need to set a ```PRIVATE_KEY``` in the .env file (e.g. this can be the same as the ```PRIVATE_KEY``` you use with the Naptha SDK). If using OpenAI, make sure to set the ```OPENAI_API_KEY``` environment variable.

You can install the module using:

```bash
poetry install
```

#### Making Changes to the Code

The main place to make changes to the code is in the ```run.py``` file. This is the default entry point that will be used when the module run is initiated. The run function can instantiate a class (e.g. an agent class) or call a function. 

#### Making Changes to the Configs

You can make changes to the configs in the ```configs``` folder. The ```deployment.json``` file is the main config file for the module. You may also have other config files for subdeployments (e.g. ```tool_deployments.json```, ```environment_deployments.json```, ```kb_deployments.json```, ```memory_deployments.json```). For example:

**MODEL**: If you would like to use a different model, you can change the ```llm_config['config_name']``` in the ```deployment.json``` file (the ```config_name``` must match the ```config_name``` in the ```llm_configs.json``` file). If using OpenAI, make sure to set the ```OPENAI_API_KEY``` environment variable.

**PERSONA**: If you would like to use a different persona, you can add ```persona_module['module_url']``` in the config dict of ```deployments.json``` file (the ```module_url``` must point to a valid Hugging Face dataset). See the [simple_chat_agent](https://github.com/NapthaAI/simple_chat_agent) module for an example of how to use a persona module with an agent.

**TOOLS**: If you would like your module to use a tool, you can add ```tool_deployments: {'name': '<tool_deployment_name>'}``` in the deployment dict of ```deployments.json``` file (the ```tool_deployment_name``` must match the ```name``` field in the ```tool_deployments.json``` file). See the [generate_image_agent](https://github.com/NapthaAI/generate_image_agent/tree/main) module for an example of how to use a tool as a subdeployment.

**ENVIRONMENT**: If you would like your module to use an environment, you can add ```environment_deployments: {'name': '<environment_deployment_name>'}``` in the deployment dict of ```deployments.json``` file (the ```environment_deployment_name``` must match the ```name``` field in the ```environment_deployments.json``` file). See the [multiagent_chat](https://github.com/NapthaAI/multiagent_chat) module for an example of how to use an environment as a subdeployment.

**KB**: If you would like your module to use a knowledge base, you can add ```kb_deployments: {'name': '<kb_deployment_name>'}``` in the deployment dict of ```deployments.json``` file (the ```kb_deployment_name``` must match the ```name``` field in the ```kb_deployments.json``` file). See the [wikipedia_agent](https://github.com/NapthaAI/wikipedia_agent/tree/main) module for an example of how to use a knowledge base as a subdeployment.

**MEMORY**: If you would like your module to use memory, you can add ```memory_deployments: {'name': '<memory_deployment_name>'}``` in the deployment dict of ```deployments.json``` file (the ```memory_deployment_name``` must match the ```name``` field in the ```memory_deployments.json``` file).

## 🧪 Testing the Module

After making changes to the module, testing usually involves the following steps:

1. Test the module locally without the Naptha Node
2. Publish the module on the Naptha Hub
3. Test running the module on a hosted Naptha Node

### 🖥️ Test the Module Locally without Node

You can run the module using:

```bash
poetry run python <module_name>/run.py
```

Now you can iterate on the module and commit your changes.

### 🌐 Publish the Module on the Naptha Hub

For this step, you will need to:

* Install the Naptha SDK using the [instructions here](https://github.com/NapthaAI/naptha-sdk). To use the SDK with your local node and hub, set ```NODE_URL=http://localhost:7001``` and ```HUB_URL=ws://localhost:3001/rpc``` in the .env file for the NapthaAI/naptha-sdk repository.

First, you need to push the module code to your GitHub or IPFS (or both). If using GitHub, make sure to change the remote origin. Also add a new module version number using e.g.:

```bash
git tag v0.1
```

To store on GitHub, you can use:

```bash
git push --tags
```

Before registering on the Naptha Hub, make sure the module field in your deployment.json file has a ```name```, ```description```, ```parameters```, ```module_type```, ```module_version```, ```module_entrypoint```, and ```execution_type``` fields:

 ```
 [
    {
      ...
        "module": {
            "name": "module_template",
            "description": "Module Template",
            "parameters": "{tool_name: str, tool_input_data: str}",
            "module_type": "agent",
            "module_version": "v0.1",
            "module_entrypoint": "run.py",
            "execution_type": "package"
        },
      ...
    }
 ]
 ```

You can register the module from a GitHub url by adding your specific repo url with the ```-r``` flag:

```bash
naptha publish -r https://github.com/NapthaAI/module_template
```

Alternatively, you can store the module on IPFS and register on the Naptha Hub by running:

```bash
naptha publish -r
```

If successful, you will see an output with the IPFS hash, and a link where you can test download via the browser http://provider.akash.pro:30584/ipfs/<ipfs_hash>.

If your module makes use of other modules (e.g. your agent module uses a tool module or memory module), you may also want to publish those sub-modules using:

```bash
naptha publish -r -s
```

Make sure to add a list of dicts with a ```name``` field to one or more of the ```agent_deployments```, ```tool_deployments```, ```environment_deployments```, ```kb_deployments```, or ```memory_deployments``` fields in your deployment.json file:

 ```
 [
    {
      ...
        "agent_deployments": [{"name": "agent_deployment_1"}],
        "tool_deployments": [{"name": "tool_deployment_1"}],
        "environment_deployments": [{"name": "environment_deployment_1"}],
        "kb_deployments": [{"name": "kb_deployment_1"}],
        "memory_deployments": [{"name": "memory_deployment_1"}],
      ...
    }
 ]
 ```

And also add corresponding ```agent_deployments.json```, ```tool_deployments.json```, ```environment_deployments.json```, ```kb_deployments.json```, or ```memory_deployments.json``` files to the ```configs``` folder for each subdeployment. In each file, there should be a module field with a ```name```, ```description```, ```parameters```, ```module_type```, ```module_version```, ```module_entrypoint```, and ```execution_type``` fields:

 ```
 [
    {
      ...
        "module": {
            "name": "subdeployment_module",
            "description": "Subdeployment Module",
            "parameters": "{tool_name: str, tool_input_data: str}",
            "module_type": "tool",
            "module_version": "v0.1",
            "module_entrypoint": "run.py",
            "execution_type": "package"
        },
      ...
    }
 ]
 ```

You can confirm that the modules were registered on the Hub by running:

```bash
naptha agents
```

Or the equivalent command for the module type you are using (e.g. ```naptha tools```, ```naptha orchestrators```, ```naptha environments```, ```naptha kbs```, ```naptha memories```, ```naptha personas```).

### ☁️ Test the Module on a hosted Node (with the hosted Naptha Hub)

Once the module is published, you can run it on a hosted Naptha Node using the Naptha SDK (to use the hosted node you should set NODE_URL=http://node.naptha.ai in the .env file):

```bash
naptha run agent:module_template -p "func_name='func', func_input_data='gm...'" 
```

Inspect the outputs to see if the module ran successfully. We aim to provide useful error outputs to help developers to debug issues with their module using the hosted node. However, you may get error outputs that are not explanatory. If this is the case, please reach out in the #support channel of our discord or via email (team@naptha.ai). 

## Run your own Naptha Node 

More extensive logs can be inspected when you run your own Naptha Node. Follow the instructions [here](https://github.com/NapthaAI/node) (still private, please reach out if you'd like access) to run your own Naptha Node. For troubleshooting, see the Troubleshooting section in NapthaAI/node for checking the logs.

## 💰 Bounties and Microgrants

Have an idea for a cool module to build? Get in touch at team@naptha.ai.

