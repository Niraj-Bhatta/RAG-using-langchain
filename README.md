# 🎥 YouTube Video Chatbot using LangChain and RAG

A basic **Retrieval-Augmented Generation (RAG)** application that lets you ask questions about a YouTube video and get answers grounded in its transcript, without watching the whole video again.

Built with **LangChain**, **Hugging Face** models, and **Sentence Transformers**.

---

## 📌 Table of Contents

- [Overview](#-overview)
- [How It Works](#-how-it-works)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Challenges and Solutions](#-challenges-and-solutions)
- [What I Learned](#-what-i-learned)
- [Limitations](#-limitations)
- [Future Improvements](#-future-improvements)
- [Contributing](#-contributing)
- [Author](#-author)

---

## 🔍 Overview

Long YouTube videos are hard to revisit when you only need one specific answer. This project uses the video's **transcript** as an external knowledge source, so the language model answers from the actual video content instead of relying only on its pre-trained knowledge.

**Example question:**

> "What are the three pieces of advice discussed in the video?"

The app retrieves the most relevant parts of the transcript and passes them to an LLM, which generates a context-aware answer.

---

## ⚙️ How It Works

The pipeline follows the four stages of RAG (**IRAG**): Indexing, Retrieval, Augmentation, Generation.

```
Transcript (transcript.txt)
        │
        ▼
 ┌─────────────┐   Clean text → split into chunks → generate embeddings
 │  1. Index   │   → store in vector store
 └──────┬──────┘
        ▼
 ┌─────────────┐   User question → similarity search
 │ 2. Retrieve │   → most relevant transcript chunks
 └──────┬──────┘
        ▼
 ┌─────────────┐   Question + retrieved chunks + instructions
 │ 3. Augment  │   → assembled into a single prompt
 └──────┬──────┘
        ▼
 ┌─────────────┐   Qwen2.5-1.5B-Instruct generates
 │ 4. Generate │   an answer based on the context
 └─────────────┘
```

### 1. Indexing
- Load the transcript
- Clean and prepare the text
- Split it into smaller chunks (so only meaningful pieces are processed per query)
- Generate embeddings for each chunk (numerical representations of semantic meaning)
- Store embeddings in a vector store

### 2. Retrieval
When a user asks a question, the system searches the vector store and returns the chunks most semantically similar to the question, rather than sending the whole transcript to the LLM.

### 3. Augmentation
The original question, the retrieved chunks, and instructions to answer using only the provided context are combined into a prompt.

### 4. Generation
The augmented prompt is sent to the LLM, which produces a natural-language answer based on the retrieved context.

---

## 🛠 Tech Stack

| Component | Technology |
|-----------|------------|
| Framework | [LangChain](https://www.langchain.com/) |
| LLM (generation) | [`Qwen/Qwen2.5-1.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct) (Hugging Face) |
| Embeddings | [`sentence-transformers/all-MiniLM-L6-v2`](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) |
| Vector store | As used in the project code |
| Language | Python |

---

## 📁 Project Structure

> Adjust file names below to match your repository.

```
RAG-using-langchain/
├── transcript.txt        # Manually copied video transcript
├── <main_script>.py      # RAG pipeline (or notebook)
├── requirements.txt      # Python dependencies
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.9+
- `pip`
- Internet connection (to download Hugging Face models on first run)
- A GPU is optional but speeds up generation

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/Niraj-Bhatta/RAG-using-langchain.git
   cd RAG-using-langchain
   ```

2. **Create and activate a virtual environment** (recommended)

   ```bash
   python -m venv venv

   # Linux / macOS
   source venv/bin/activate

   # Windows
   venv\Scripts\activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

   If you don't have a `requirements.txt`, the core packages are typically:

   ```bash
   pip install langchain langchain-community langchain-huggingface sentence-transformers transformers torch faiss-cpu
   ```

   Install whichever vector store package your code uses.

---

## ▶️ Usage

1. **Get the transcript.** Open a YouTube video, copy its transcript, and paste it into `transcript.txt` in the project root.

2. **Run the application.**

   ```bash
   python <main_script>.py
   ```

   *(or open and run the notebook cells in order)*

3. **Ask questions** about the video.

   ```
   Question: What are the three pieces of advice discussed in the video?
   Answer:   (generated from the retrieved transcript chunks)
   ```

> The first run downloads the Qwen and MiniLM models from Hugging Face, which may take a few minutes.

---

## 🧩 Challenges and Solutions

### 1. OpenAI API quota limits
**Problem:** The available OpenAI API quota ran out after only a few requests.

**Solution:** Switched to open-source Hugging Face models:
- `Qwen/Qwen2.5-1.5B-Instruct` for response generation
- `sentence-transformers/all-MiniLM-L6-v2` for embeddings

This removed the dependency on a paid API and made it easy to experiment with open-source models in a RAG setup.

### 2. YouTube transcript fetching blocked
**Problem:** After repeated programmatic transcript requests, YouTube temporarily blocked the IP, so transcripts could no longer be fetched through code.

**Solution:** Use a manual workflow: copy the transcript, save it as `transcript.txt`, load it into the app, then continue with cleaning, chunking, embedding, and indexing.

---

## 📚 What I Learned

- How LangChain connects the components of a RAG application
- How document loading, text splitting, and embeddings work together
- Why vector stores are useful for semantic search
- How retrieval supplies external context to an LLM
- How augmentation guides the model's response
- How to integrate Hugging Face models into a practical application
- Why troubleshooting and finding alternatives matter during development

Building a RAG app isn't just connecting an LLM to a vector database. Each stage has a specific responsibility, and answer quality depends on how well the stages work together.

---

## ⚠️ Limitations

- The transcript must be provided manually
- Qwen2.5-1.5B is a small model, so answers may be less accurate or detailed than larger LLMs
- Answers are limited to what the transcript contains
- Retrieval quality depends on chunk size, overlap, and the embedding model

---

## 🔮 Future Improvements

- [ ] Automate transcript extraction
- [ ] Experiment with different embedding models
- [ ] Improve retrieval quality (chunk tuning, re-ranking)
- [ ] Try larger or alternative LLMs
- [ ] Explore advanced RAG techniques
- [ ] Add a simple web interface (e.g., Streamlit or Gradio)

---

## 🤝 Contributing

Suggestions and improvements are welcome.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m "Add your feature"`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

---

## 👤 Author

**Niraj Bhatta**
GitHub: [@Niraj-Bhatta](https://github.com/Niraj-Bhatta)

If you found this project useful, consider giving it a ⭐ and sharing your feedback!
