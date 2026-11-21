# RAG Workshop with the Annals of King Sejong

[한국어](README.md) · **English**

Build a **Retrieval-Augmented Generation (RAG)** system using XML articles from the Annals of King Sejong. This workshop alternates between conceptual explanations and Google Colab exercises, following historical texts through chunking, embedding, retrieval, and answer generation.

The emphasis is on **designing a system around its data**. Understanding historical context, preserving meaning across chunks, and assessing answers against source evidence are places where humanities expertise contributes directly to AI system design.

**This English README is a guide to the materials. The slides, notebook explanations, comments, and intended answers are in Korean; article bodies are primarily in Classical Chinese.**

## Get started

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/littlekg87/RAG_Workshop/blob/main/세종실록_RAG_7단계_실습.ipynb)

| Material | Contents |
| --- | --- |
| [View the presentation online](https://littlekg87.github.io/RAG_Workshop/) | 76 interactive slides; no download or installation required |
| [Download the HTML](https://github.com/littlekg87/RAG_Workshop/raw/refs/heads/main/세종실록_RAG_발표자료.html) | A single HTML file for offline presentations |
| [Colab notebook](세종실록_RAG_7단계_실습.ipynb) | Seven stages with Korean explanations and code comments |
| [Download the XML ZIP](https://github.com/littlekg87/RAG_Workshop/raw/refs/heads/main/sejong_xml.zip) | 33 chronological XML files used in the exercises |

**[Open the presentation in your browser](https://littlekg87.github.io/RAG_Workshop/).** For offline use, download the HTML file and open it locally. Fonts, figures, and demonstration data are embedded in the HTML; no web server is required. Keep the HTML, notebook, and ZIP in the same folder to use the presentation's local download links.

## Setup and execution

1. Open the notebook with **Open in Colab**. A Google account and internet access are required.
2. Before running any code, choose **Runtime → Change runtime type → T4 GPU**.
3. Download `sejong_xml.zip` to your computer.
4. Run the notebook **in order, starting with cell 1-1**. Check the installation success message before continuing.
5. Upload the single file `sejong_xml.zip` when prompted in **cell 2-1**.
6. Complete chunking, embedding, retrieval, and generation, then ask questions through Gradio in **stage 7**.

Public models are downloaded and executed directly in Colab. **No paid model API, API key, or Google Drive mount is used.** The workshop can run without paid APIs when a free Colab GPU is available, but GPU allocation and session duration are not guaranteed. If the runtime is deleted, prepare the models and data again.

## Learning path

The presentation has six sections:

1. RAG, hallucinations, and retrieval–augmentation–generation
2. Library installation and GPU model setup
3. XML structure, chunking, metadata, embeddings, and a vector index
4. Query embeddings, cosine similarity and dot products, top-k, prompts, and generation
5. Connecting the Gradio frontend to the retrieval and generation backend
6. Retrieval failures versus generation failures, improvement options, and dependencies between stages

Slides include complete notebook cells, copy buttons, code-explanation dialogs, and interactive diagrams. Conceptual illustrations are explicitly distinguished from recorded embedding and retrieval results.

## Seven practical stages

| Stage | Colab cells | Task |
| --- | --- | --- |
| 1. Setup | 1-1, 1-2 | Install libraries, check the GPU, download and load E5 and Qwen3 |
| 2. XML chunking | 2-1, 2-2 | Upload the ZIP, extract articles, split long bodies, preserve metadata |
| 3. Vector index | 3-1, 3-2, 3-3 | Embed chunks → inspect and save the array → build a FAISS index |
| 4. Query embedding | 4 | Embed and normalize a question with the same E5 model |
| 5. Retrieval | 5 | Use dot products of normalized vectors to retrieve the top-k chunks |
| 6. Answer generation | 6 | Combine instructions, retrieved evidence, and the question for Qwen3 |
| 7. Frontend | 7 | Accept a question and top-k in Gradio; display the answer and supplied evidence |

Preprocessing raw data into chunkable XML has already been completed. The exercises begin with the supplied XML articles.

## Models and execution

| Component | Role | Runs on |
| --- | --- | --- |
| `intfloat/multilingual-e5-small` | Encode chunks and queries as 384-dimensional vectors | GPU |
| `Qwen/Qwen3-1.7B` | Generate Korean answers from evidence and a question | GPU, float16 |
| FAISS `IndexFlatIP` | Exact dot-product search over normalized vectors | CPU |
| Gradio | Connect browser inputs and outputs to Python functions | Colab server / browser UI |

Documents use the `passage: ` prefix; queries use `query: `. Both vectors are normalized to unit length, so **their dot product equals cosine similarity**. FAISS does not need to compute angles or rotate vectors.

The base exercise uses pretrained models without additional training. Fine-tuning, hybrid retrieval, and reranking are discussed as possible extensions.

## Corpus and defaults

- **Coverage:** 33 chronological XML files, from Sejong's accession year through his 32nd regnal year. Supplementary sections such as ritual, music, and geography are excluded from the exercise ZIP.
- **Articles:** 30,948. The full chronological corpus is processed, without a small-article subset limit.
- **Search text:** Korean article title plus Classical Chinese body.
- **Chunking:** 350 body tokens with 40-token overlap, using the E5 tokenizer. Chunks do not cross article boundaries.
- **Chunks:** 37,126 with the supplied ZIP and default settings; changing either can change the count.
- **Metadata:** Article ID, title, date code, subjects, edition/volume/page, filename, and part numbers.
- **Embeddings:** Batch size 32, 384 dimensions, unit-length normalization.
- **Retrieval:** Default `top-k=3`; the Gradio slider allows 1–5.
- **Generation:** Retrieved evidence is limited to 5,000 Qwen tokens, and output to 256 new tokens. Instructions, the question, and the chat template are added separately from the evidence budget.

Date codes and source descriptions retain their original values. For example, `1418-08-11L0` must not be treated as a Gregorian date without interpretation. Consult the [National Institute of Korean History's Annals service](https://sillok.history.go.kr/) when checking historical texts and translations.

## Interpreting results

A similarity score is **not a probability that an answer is correct**. Retrieval can rank a different but similar event highly. Even with the correct passage, a small language model may misread Classical Chinese or reverse an exception condition.

The sources displayed by Gradio are **the retrieved evidence actually passed to the model**. They do not certify every claim in the answer. The interface does not retain conversation history: each new question triggers retrieval and generation again.

Improvement can involve source data, chunking, embeddings, query rules, indexing and retrieval, evidence assembly, and the generator. Changes to one stage also affect the inputs and limitations of other stages.

## File layout

```text
RAG_Workshop/
├── README.md
├── README.en.md
├── 세종실록_RAG_발표자료.html
├── 세종실록_RAG_7단계_실습.ipynb
└── sejong_xml.zip
```

This layout places the five files at the repository root. `Source/`, `references/`, and `benchmark/` are supplementary authoring and validation folders; participants do not need them for the base exercise. Authoring scripts in `references/` contain local paths from the production environment and require adjustment before rebuilding elsewhere.

## Author and related resources

**김근하 · KUNHA KIM** — [Portfolio](https://kunslab.com/)

- [Earlier DH2026 RAG workshop materials](https://github.com/littlekg87/DH2026/tree/main/Workshop_RAG)
- [Multilingual E5-small](https://huggingface.co/intfloat/multilingual-e5-small)
- [Qwen3-1.7B](https://huggingface.co/Qwen/Qwen3-1.7B)
- [FAISS](https://github.com/facebookresearch/faiss)
- [Gradio](https://www.gradio.app/)
