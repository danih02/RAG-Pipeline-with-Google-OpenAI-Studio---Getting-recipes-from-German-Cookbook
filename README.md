# RAG Pipeline with Google OpenAI Studio: Extracting Recipes from a German Cookbook

This project demonstrates a Retrieval-Augmented Generation (RAG) pipeline, orchestrated by an intelligent agent built with [LangChain](https://github.com/langchain-ai/langchain) and powered by Google's Gemini LLM (via OpenAI Studio). The system extracts and queries recipes from the German cookbook **"Das gute Essen – Mit 500 Rezepten für jeden Tag"**

## Table of Contents

- [Overview](#overview)
- [Agent & RAG Architecture](#agent--rag-architecture)
- [Features](#features)
- [Installation](#installation)
- [How It Works](#how-it-works)
- [Usage Examples](#usage-examples)
- [Customization](#customization)
- [Limitations](#limitations)
- [Acknowledgements](#acknowledgements)

---

## Overview

The goal of this project is to enable users to ask questions about recipes in a scanned German cookbook and receive structured, context-aware answers. The pipeline combines classical keyword-based retrieval (BM25), semantic search (FAISS with sentence embeddings), and a powerful LLM agent to extract and summarize recipes—even when multiple recipes appear on a single page.

## Agent & RAG Architecture

At the heart of this project is an **agent** that coordinates the RAG workflow:

- **Hybrid Retrieval:** The agent uses both BM25 (keyword search) and FAISS (semantic search) to retrieve the most relevant recipe contexts from the cookbook.
- **Context Assembly:** Retrieved passages are merged and filtered for relevance.
- **Prompt Engineering:** The agent constructs a prompt that strictly instructs the LLM to answer only using the provided context, and always in German.
- **LLM Orchestration:** The agent invokes Gemini 1.5 Pro via Google OpenAI Studio, ensuring answers are grounded in the cookbook and not hallucinated.
- **Relevance Filtering:** The agent checks if the context is sufficient before generating an answer, and can abstain if the context is not relevant.

This agent-driven RAG setup ensures robust, context-aware, and trustworthy answers to user queries about the cookbook.

## Features

- **PDF Parsing:** Loads and processes the cookbook PDF page-wise.
- **Text Block Extraction:** Extracts text blocks with position and font size for logical reading order.
- **Recipe Segmentation:** Splits pages into individual recipes using heuristics and text size.
- **Hybrid Retrieval:** Combines BM25 and FAISS for robust document retrieval.
- **Agent-Orchestrated RAG Pipeline:** An agent manages retrieval, context assembly, and LLM prompting for accurate, context-based answers.
- **LLM Integration:** Utilizes Gemini 1.5 Pro via Google OpenAI Studio for natural language understanding and generation.
- **Relevance Filtering:** Ensures answers are only given when the context is relevant.

## Installation

1. **Clone the repository** and place the cookbook PDF (`Das gute Essen - Kochbuch.pdf`) in the root directory.

2. **Install dependencies:**
    ```sh
    pip install langchain pypdf faiss-cpu sentence-transformers transformers langchain-community langchain_google_genai PyMuPDF rank_bm25 matplotlib pillow
    ```

3. **Set up your Google API key:**
    - Obtain an API key for Google Generative AI (Gemini) via OpenAI Studio.
    - Set the environment variable:
      ```sh
      export GOOGLE_API_KEY=your_api_key_here
      ```

## How It Works

1. **PDF Loading:** The PDF is loaded and each page is parsed into text blocks with coordinates and font sizes.
2. **Reading Order:** Pages are split into columns (left, middle, right) for logical reading order.
3. **Recipe Detection:** Headers are identified by font size, and recipes are segmented using custom tokens.
4. **Recipe Extraction:** Pages with multiple recipes are split into individual recipes.
5. **Vector Database:** All recipes are embedded and stored in a FAISS vector database.
6. **Hybrid Retrieval:** The agent combines BM25 and FAISS to retrieve relevant recipes.
7. **Prompting:** The agent crafts a structured prompt so the LLM only uses provided context and answers in German.
8. **Relevance Filtering:** The agent ensures the LLM only answers if the retrieved context is relevant.

## Usage Examples

- **Query a specific page:**
    ```python
    query = 'Erzähl mit etwas über das Rezept auf der Seite.'
    context = documents[page_index]
    output = chain_example.invoke({'input_prompt': query, 'context': context}).content
    print(output)
    ```

- **Ask for a recipe with specific ingredients:**
    ```python
    query = "Ich habe Paprika, Eier und Gurke. Gib mir Rezepte, die mindestens zwei davon enthalten."
    print(rag_chain.invoke(query)['answer'])
    ```

- **Stream LLM output:**
    ```python
    for chunk in llm_2.stream("Write a short poem about asynchronous programming."):
        print(chunk.content, end="", flush=True)
    ```

## Customization

- **Change the prompt template** to adjust answer structure or language.
- **Tune retriever weights** for BM25 and FAISS in the `EnsembleRetriever`.
- **Swap embedding models** for different semantic search behavior.

## Limitations

- The pipeline relies on heuristics for recipe segmentation; results may vary with different cookbook layouts.
- The LLM is instructed to only use provided context, but hallucinations are still possible.
- The index of recipes in the final list does not correspond to the original PDF page numbers.

## Acknowledgements

- [LangChain](https://github.com/langchain-ai/langchain)
- [Google Generative AI (Gemini)](https://ai.google.dev/)
- [FAISS](https://github.com/facebookresearch/faiss)
- [PyMuPDF](https://github.com/pymupdf/PyMuPDF)
- Cookbook: *Das gute Essen – Mit 500 Rezepten für jeden Tag*

---

*For questions or contributions, please open an issue or pull request!*
