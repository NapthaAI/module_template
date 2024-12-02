# Module Template

This is a base module template for creating agent, agent orchestrator, and environment modules. You can check out other examples of agent, orchestrator and environment modules using the CLI commands with the [Naptha SDK](https://github.com/NapthaAI/naptha-sdk). 

## Pre-Requisites 

### Install the Naptha SDK and create .env

Install the Naptha SDK using the [instructions here](https://github.com/NapthaAI/naptha-sdk).

### (Optional) Run your own Naptha Node

You can run your own Naptha node using the [instructions here](https://github.com/NapthaAI/node) (still private, please reach out if you'd like access).

### Set the environment variables

Create a copy of the .env file:

```bash
cp .env.example .env
```

Choose whether you want to interact with a local Naptha node or a hosted Naptha node. For a local node, set ```NODE_URL=http://localhost:7001``` in the .env file. To use a hosted node, set ```NODE_URL=http://node.naptha.ai:7001``` or ```NODE_URL=http://node1.naptha.ai:7001```. If your module calls others modules (e.g. using Agent(), Tool(), or Environment()), you need to set a ```PRIVATE_KEY``` in the .env file (e.g. this can be the same as the ```PRIVATE_KEY``` you use with the Naptha SDK). If using OpenAI, make sure to set the ```OPENAI_API_KEY``` environment variable.

### Install Poetry 

From the official poetry [docs](https://python-poetry.org/docs/#installing-with-the-official-installer):

```bash
curl -sSL https://install.python-poetry.org | python3 -
export PATH="/home/$(whoami)/.local/bin:$PATH"
```

## Running the Module on a Naptha Node

Using the Naptha SDK:

```bash
naptha run agent:module_template -p "func_name='func', func_input_data='gm...'" 
```

## Making Changes to the Module

Before deploying a new or updated module to a Naptha node, you should iterate on improvements with the module locally. 

### Clone and Install the Module

Clone the repo using:

```bash
git clone https://github.com/NapthaAI/<module_name>
cd <module_name>
```

You can install the module using:

```bash
poetry install
```

### Making Changes to the Code

The main place to make changes to the code is in the ```run.py``` file. This is the default entry point that will be used when the module run is initiated. The run function can instantiate a class (e.g. an agent class) or call a function. 

### Making Changes to the Configs

You can make changes to the configs in the ```configs``` folder. For example:

**MODEL**: If you would like to use a different model, you can change the ```llm_config['config_name']``` in the ```agent_deployments.json``` file (the ```config_name``` must match the ```config_name``` in the ```llm_configs.json``` file).

**PERSONA**: If you would like to use a different persona, you can change the ```persona_module['url']``` in the ```agent_deployments.json``` file (the ```url``` must point to a valid Hugging Face dataset).

### Running the Module Locally

You can run the module using:

```bash
poetry run python <module_name>/run.py
```

Now you can iterate on the module and commit your changes.

## Register the new or updated Module on the Naptha Hub

When ready, you can push to your GitHub account or IPFS (or both). Make sure to change the remote origin. Also add a new version number using e.g.:

```bash
git tag v0.1
```

```bash
git push --tags
```

If creating an agent module, you can register it on the Naptha Hub using:

```bash
naptha agents agent_name -p "description='Agent description' url='ipfs://QmNer9SRKmJPv4Ae3vdVYo6eFjPcyJ8uZ2rRSYd3koT6jg' type='package' version='0.1'" 
```

If creating an orchestrator module, you can register it on the Naptha Hub using:

```bash
naptha orchestrators orchestrator_name -p "description='Orchestrator description' url='ipfs://QmNer9SRKmJPv4Ae3vdVYo6eFjPcyJ8uZ2rRSYd3koT6jg' type='package' version='0.1'" 
```

If creating an environment module, you can register it on the Naptha Hub using:

```bash
naptha environments environment_name -p "description='Environment description' url='ipfs://QmNer9SRKmJPv4Ae3vdVYo6eFjPcyJ8uZ2rRSYd3koT6jg' type='package' version='0.1'" 
```

Make sure to replace the placeholder descriptions and URLs with your own.
