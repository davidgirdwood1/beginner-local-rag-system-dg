# 🪟 Windows & Intel CPU Setup Guide for Local RAG

This guide provides the necessary steps to run this Local RAG system on a **Windows 11** environment using an **Intel CPU** (integrated graphics), bypassing the need for an NVIDIA GPU or macOS/Homebrew dependencies.

## 🛠 Prerequisites

Before starting, ensure you have the following installed on your system:

1.  **Python 3.11.x**: (Recommended for stable ML dependency support). Use the [Python Launcher for Windows](https://www.python.org/downloads/windows/).
2.  **Tesseract OCR Engine**: Required for image and PDF text extraction.
    * Download the installer from [UB Mannheim's Tesseract page](https://github.com/UB-Mannheim/tesseract/wiki).
    * **Note:** Add the installation path (e.g., `C:\Program Files\Tesseract-OCR`) to your System Environment **PATH**.
3.  **Poppler for Windows**: Required for PDF rendering.
    * Download the latest binaries from [oschwartz10612/poppler-windows](https://github.com/oschwartz10612/poppler-windows/releases/).
    * Extract and add the `/bin` folder to your System **PATH**.

---

## 🚀 Configuration & Optimization

To achieve high accuracy on a CPU-only machine, the following architectural adjustments were implemented:

### 1. CPU-Optimized Torch

The standard `pip install torch` often defaults to large CUDA binaries. For Intel integrated graphics, install the CPU-specific build:

```
pip install torch==2.4.1 --index-url https://download.pytorch.org/whl/cpu
```

### 2. Local Embedding Model Setup

To ensure the system remains 100% local and avoids latency/connectivity issues, the embedding model should be pre-downloaded:

- **Model**: `sentence-transformers/all-mpnet-base-v2`
- Download the model files with Powershell and place them in an `/embedding_model` folder in the project root. `hf download sentence-transformers/all-mpnet-base-v2`
- Update `constants.py`:

```python
EMBEDDING_MODEL_PATH = "./embedding_model/"
```

### 3. Hyperparameter Tuning (Chunking)

Small chunk sizes can lead to context fragmentation, causing the LLM to hallucinate. For technical or policy documents, these settings proved most effective:

- `TEXT_CHUNK_SIZE`: 1000  
- `TEXT_CHUNK_OVERLAP`: 200  

### 4. LLM Selection (Ollama)

For a laptop with integrated graphics, `gemma3:1b` is the recommended model for its low memory footprint and strong reasoning capabilities for its size.

- Install Ollama for Windows.
- Run:

```bash
ollama pull gemma3:1b
```

## 🎨 UI & Environment Settings

### Forced Light Mode

To resolve contrast issues with Streamlit's "Emotion Cache" in various Windows themes, it is recommended to force the Light theme for consistent visibility of status messages and file management text.

Update `.streamlit/config.toml`:

```toml
[theme]
base="light"
primaryColor="#d33682"
```

## 🧪 Verification Test

To verify the RAG pipeline is correctly retrieving local data rather than relying on pre-trained knowledge, try a "Stress Test" with a fictional policy document:

- **Upload**: A document with unique, nonsensical rules (e.g., "Employees must wear violet on Thursdays").
- **Enable RAG Mode**: Ensure the checkbox is checked.
- **Query**: Ask about the specific rule.
- **Result**: The system should cite the specific text chunk and page number from your uploaded PDF.

---

Developed as a portfolio piece to demonstrate full-stack AI/ML integration on standard consumer hardware.
