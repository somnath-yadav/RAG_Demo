# Retrieval-Augmented Generation (RAG) Example

This repository contains code for demonstrating retrieval-augmented
generation (RAG), a mechanism for incorporating domain-specific
content into generative AI interactions with large language models
(LLMs).



## LangChain

This project depends on the open source
[LangChain](https://www.langchain.com/) library which provides
abstractions and orchestration on top of the these features (among others):

- Document indexing and vector embedding
- Prompt customization, prompt templates, task-specific prompts
- Support for a diverse set of LLMs and LLM interface APIs
- Memory support
- Multi-stage pipelines/chains integrating all features and multiple invokation

The following specifics can be easily changed to other formats,
models, and providers as needed:

- Domain document format, converter, and splitter: PDF and
  [`PyPDFLoader`](https://python.langchain.com/docs/modules/data_connection/document_loaders/pdf)
- Choice of vector embedding provider and model: Huggingface
  [`all-MiniLM-L6-v2`](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)
- Choice of vector embedding persistence database:
  [Chroma](https://python.langchain.com/docs/integrations/vectorstores/chroma)
- Choice of Large Language Model provider and model: 
  - [Azure OpenAI](https://python.langchain.com/docs/integrations/llms/azure_openai)
  - [Amazon Bedrock](https://api.python.langchain.com/en/latest/chat_models/langchain.chat_models.bedrock.BedrockChat.html)

## Document Indexing

Document indexing by generated vector embeddings provides a
cost-effective strategy for integrating domain-specific information
into large language model interactions. It allows for domain-driven
behavior on top of general purpose LLMs&mdash;the most common
case&mdash;and is consistent and compatible with special purpose and
fine-tuned LLMs.

The first step in setting up RAG is to perform a pre-processing step
to index the domain-specific document text:

- Convert documents into pure text (if needed)
- Break text into appropriately sized chunks
- Generate vector embeddings (a numeric representation of the text
  that represents the semantic information in the text)
- Store the chunks indexed by embedding into a persistent database for
  matchiing and retrieval.

This example uses a PDF document of [The Federalist
Papers](https://en.wikipedia.org/wiki/The_Federalist_Papers) as the
source document, but there are additional langchain document ingesting
tools for Word documents and many other document types. You can also
use the langchain `Document` object inside your custom text ingestion
code.

Document ingesting tools store metadata along with each chunk of text
in the langchain `Document` object. In the case of the `PyPDFLoader`
it saves the name of the PDF file and the page number of the chunk
within the PDF file. You can add your own metadata to document chunks
before you persist them and the metadata can be used by the chain and
calling applications, for example, to provide a reference link to the
materials used for generation.

It is not necessary to use the same language model for generating
vector embedding indexes that is used for generating the final textual
response. In our case we are using a much smaller and less
resources-intensive HuggingFace model called `all-MiniLM-L6-v2`.  It
**is** necessary to use the same embeddings model for the retrieval
part of the process that was used to create the persisted vector
embeddings, so you will see this same model used in the Testing
Retrieval section and in the full retrieval chain and chatbot.

