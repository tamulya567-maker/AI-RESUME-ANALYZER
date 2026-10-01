# AI Resume Analyzer / Smart RAG Assistant

A simple **Retrieval-Augmented Generation (RAG)** project built in Google Colab. The program uploads a PDF document, extracts its text, splits the text into chunks, creates embeddings, stores them in a FAISS vector index, retrieves relevant chunks for a question, and uses **FLAN-T5 Base** to generate an answer from the retrieved context.

## Features

- Upload a PDF directly in Google Colab
- Extract text from the PDF using `pypdf`
- Split the document into overlapping chunks
- Generate text embeddings using `all-MiniLM-L6-v2`
- Store embeddings in a FAISS vector database
- Retrieve the top relevant chunks for a user question
- Generate answers using `google/flan-t5-base`
- Interactive question-and-answer loop
- Prevents unsupported answers by instructing the model to respond when information is not available in the document

## RAG Workflow

```text
PDF Upload
    ↓
Text Extraction
    ↓
Text Chunking
    ↓
Sentence Embeddings
    ↓
FAISS Vector Database
    ↓
User Question
    ↓
Similarity Search
    ↓
Top Relevant Chunks
    ↓
RAG Prompt
    ↓
FLAN-T5
    ↓
Answer
```

## Technologies Used

- Python
- Google Colab
- PyPDF
- Sentence Transformers
- FAISS
- Hugging Face Transformers
- FLAN-T5

## Requirements

The notebook installs the required packages with:

```bash
pip install -q sentence-transformers faiss-cpu pypdf transformers accelerate
```

## Models Used

### Embedding Model

```text
sentence-transformers/all-MiniLM-L6-v2
```

This model converts document chunks and questions into numerical vectors so that similar content can be retrieved.

### Language Model

```text
google/flan-t5-base
```

This model generates the final answer using the retrieved document context.

## How to Run

### 1. Open Google Colab

Create a new Google Colab notebook and copy the Python code from:

```text
ai_resume_analyzer.py
```

### 2. Install Dependencies

Run:

```python
!pip install -q sentence-transformers faiss-cpu pypdf transformers accelerate
```

### 3. Upload a PDF

Run the notebook cell that contains:

```python
uploaded = files.upload()
```

Select the PDF you want the RAG system to analyze.

### 4. Extract Text

The program reads every page of the uploaded PDF and extracts available text using `PdfReader`.

### 5. Create Chunks

The document is divided into chunks using:

```python
create_chunks(text, chunk_size=500, overlap=100)
```

The default configuration uses a chunk size of 500 characters and an overlap of 100 characters.

### 6. Create Embeddings

The chunks are converted into embeddings with:

```python
SentenceTransformer("all-MiniLM-L6-v2")
```

### 7. Create the FAISS Vector Database

The embeddings are stored in a FAISS `IndexFlatL2` index. This allows the system to search for chunks that are close to the user's question in embedding space.

### 8. Ask Questions

The program starts an interactive assistant:

```text
======================================
       📚 SMART RAG ASSISTANT
======================================
Ask questions about your uploaded PDF.
Type 'exit' to stop.
```

Example:

```text
You: What are the main rules mentioned in the document?
```

The system retrieves the three most relevant chunks and sends them to the language model.

### 9. Stop the Assistant

Type:

```text
exit
```

to stop the chatbot.

## Important Function

The main RAG function is:

```python
generate_answer(question, top_k=3)
```

It performs three important steps:

1. Retrieves relevant chunks from FAISS.
2. Combines the chunks into context.
3. Sends the context and question to FLAN-T5.

The prompt instructs the model to answer only from the provided context. If the information cannot be found, it should respond:

```text
The answer is not available in the document.
```

## Project Structure

```text
AI-Resume-Analyzer/
│
├── ai_resume_analyzer.py
├── README.md
└── sample.pdf
```

`sample.pdf` is optional and represents the document that the user uploads during execution.

## Example Use Cases

Although the current script is named `ai_resume_analyzer.py`, the implemented RAG pipeline can be used with many types of PDF documents, such as:

- Resumes
- Company policies
- Educational notes
- Reports
- Manuals
- Research documents
- Project documentation

For resume analysis specifically, the uploaded PDF can contain a resume and the user can ask questions about information present in that document.

## Limitations

- The system works with text that can be extracted from the PDF. Image-only/scanned PDFs may require OCR.
- The current chunking method is character-based rather than semantic.
- FAISS data is created during the current notebook session and is not persisted by this script.
- The model may have limited answer quality compared with larger modern language models.
- The system currently retrieves a fixed number of top chunks (`top_k=3` by default).
- The program is designed as a simple educational RAG implementation rather than a production application.

## RAG Components in This Project

| Component | Technology |
|---|---|
| Document input | PDF |
| Text extraction | PyPDF |
| Chunking | Custom Python function |
| Embeddings | all-MiniLM-L6-v2 |
| Vector database | FAISS |
| Retrieval | L2 similarity search |
| Generation | FLAN-T5 Base |
| Interface | Command-line chatbot |
| Environment | Google Colab |

## Future Improvements

Possible improvements include:

- Add a web-based user interface with Streamlit or Gradio
- Add OCR for scanned PDFs
- Use better semantic chunking
- Store FAISS indexes permanently
- Add metadata and source references
- Support multiple PDF documents
- Add conversation memory
- Add resume-specific extraction such as skills, education, and experience
- Add document comparison
- Improve retrieval with hybrid search
- Add evaluation metrics for retrieval and answer quality

## Educational Goal

This project demonstrates the basic RAG pipeline:

**Retrieve relevant information first, then generate an answer using that information.**

It is useful for learning how document ingestion, chunking, embeddings, vector search, retrieval, and LLM generation work together in a RAG application.

## Source

The Python implementation for this README is based on the uploaded `ai_resume_analyzer.py` file, which contains the complete PDF ingestion, chunking, embedding, FAISS retrieval, and FLAN-T5 generation pipeline.
