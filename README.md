# RAG Pipeline with Google OpenAI Studio: Extracting Recipes from a German Cookbook

This project demonstrates how to use a Retrieval-Augmented Generation (RAG) pipeline, powered by [LangChain](https://github.com/langchain-ai/langchain) and Google's Gemini LLM (via OpenAI Studio), to extract and query recipes from the German cookbook **"Das gute Essen – Mit 500 Rezepten für jeden Tag"**.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [How It Works](#how-it-works)
- [Usage Examples](#usage-examples)
- [Customization](#customization)
- [Limitations](#limitations)
- [Acknowledgements](#acknowledgements)

---

## Overview

The goal of this project is to enable users to ask questions about recipes in a scanned German cookbook and receive structured, context-aware answers. The pipeline combines classical keyword-based retrieval (BM25), semantic search (FAISS with sentence embeddings), and a powerful LLM to extract and summarize recipes—even when multiple recipes appear on a single page.

## Features

- **PDF Parsing:** Loads and processes the cookbook PDF page-wise.
- **Text Block Extraction:** Extracts text blocks with position and font size for logical reading order.
- **Recipe Segmentation:** Splits pages into individual recipes using heuristics and text size.
- **Hybrid Retrieval:** Combines BM25 and FAISS for robust document retrieval.
- **RAG Pipeline:** Uses LangChain to provide context-aware answers, strictly based on the cookbook content.
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
6. **Hybrid Retrieval:** BM25 and FAISS are combined for robust retrieval.
7. **Prompting:** A structured prompt ensures the LLM only uses provided context and answers in German.
8. **Relevance Filtering:** The LLM judges if the retrieved context is relevant before answering.

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
