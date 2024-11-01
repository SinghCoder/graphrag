# Getting Started

## Requirements

[Python 3.10-3.12](https://www.python.org/downloads/)

To get started with the GraphRAG system, you have a few options:

👉 [Use the GraphRAG Accelerator solution](https://github.com/Azure-Samples/graphrag-accelerator) <br/>
👉 [Install from pypi](https://pypi.org/project/graphrag/). <br/>
👉 [Use it from source](developing.md)<br/>

## Quickstart

To get started with the GraphRAG system we recommend trying the [Solution Accelerator](https://github.com/Azure-Samples/graphrag-accelerator) package. This provides a user-friendly end-to-end experience with Azure resources.

# Top-Level Modules

* [Indexing Pipeline Overview](index/overview.md)
* [Query Engine Overview](query/overview.md)

# Overview

The following is a simple end-to-end example for using the GraphRAG system.
It shows how to use the system to index some text, and then use the indexed data to answer questions about the documents.

# Install GraphRAG

```bash
pip install graphrag
```

The graphrag library includes a CLI for a no-code approach to getting started. Please review the full [CLI documentation](cli.md) for further detail.

# Running the Indexer

Now we need to set up a data project and some initial configuration. Let's set that up. We're using the [default configuration mode](config/overview.md), which you can customize as needed using a [config file](config/json_yaml.md), which we recommend, or [environment variables](config/env_vars.md).

First let's get a sample dataset ready:

```sh
mkdir -p ./ragtest/input
```

Now let's get a copy of A Christmas Carol by Charles Dickens from a trusted source

```sh
curl https://www.gutenberg.org/cache/epub/24022/pg24022.txt -o ./ragtest/input/book.txt
```

Next we'll inject some required config variables:

## Set Up Your Workspace Variables

First let's make sure to setup the required environment variables. For details on these environment variables, and what environment variables are available, see the [variables documentation](config/overview.md).

To initialize your workspace, first run the `graphrag init` command.
Since we have already configured a directory named `./ragtest` in the previous step, run the following command:

```sh
graphrag init --root ./ragtest
```

This will create two files: `.env` and `settings.yaml` in the `./ragtest` directory.

- `.env` contains the environment variables required to run the GraphRAG pipeline. If you inspect the file, you'll see a single environment variable defined,
  `GRAPHRAG_API_KEY=<API_KEY>`. This is the API key for the OpenAI API or Azure OpenAI endpoint. You can replace this with your own API key. If you are using another form of authentication (i.e. managed identity), please delete this file.
- `settings.yaml` contains the settings for the pipeline. You can modify this file to change the settings for the pipeline.
  <br/>

#### <ins>OpenAI and Azure OpenAI</ins>

If running in OpenAI mode, update the value of `GRAPHRAG_API_KEY` in the `.env` file with your OpenAI API key.

#### <ins>Azure OpenAI</ins>

In addition, Azure OpenAI users should set the following variables in the settings.yaml file. To find the appropriate sections, just search for the `llm:` configuration, you should see two sections, one for the chat endpoint and one for the embeddings endpoint. Here is an example of how to configure the chat endpoint:

```yaml
type: azure_openai_chat # Or azure_openai_embedding for embeddings
api_base: https://<instance>.openai.azure.com
api_version: 2024-02-15-preview # You can customize this for other versions
deployment_name: <azure_model_deployment_name>
```

- For more details about configuring GraphRAG, see the [configuration documentation](config/overview.md).
- To learn more about Initialization, refer to the [Initialization documentation](config/init.md).
- For more details about using the CLI, refer to the [CLI documentation](query/cli.md).

## Running the Indexing pipeline

Finally we'll run the pipeline!

```sh
graphrag index --root ./ragtest
```

![pipeline executing from the CLI](img/pipeline-running.png)

This process will take some time to run. This depends on the size of your input data, what model you're using, and the text chunk size being used (these can be configured in your `settings.yml` file).
Once the pipeline is complete, you should see a new folder called `./ragtest/output` with a series of parquet files.

# Using the Query Engine

## Running the Query Engine

Now let's ask some questions using this dataset.

Here is an example using Global search to ask a high-level question:

```sh
graphrag query \
--root ./ragtest \
--method global \
--query "What are the top themes in this story?"
```

Here is an example using Local search to ask a more specific question about a particular character:

```sh
graphrag query \
--root ./ragtest \
--method local \
--query "Who is Scrooge and what are his main relationships?"
```

Please refer to [Query Engine](query/overview.md) docs for detailed information about how to leverage our Local and Global search mechanisms for extracting meaningful insights from data after the Indexer has wrapped up execution.