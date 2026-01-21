# Jarvis AI Assistant

A personal AI assistant powered by Google Gemini (or Ollama as fallback) with knowledge storage using ChromaDB vector database and a modern web UI.

## Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Web Frontend  │────▶│  FastAPI Server │────▶│  Google Gemini  │
│   (HTML/CSS/JS) │     │   (Backend)     │     │   (or Ollama)   │
└─────────────────┘     └────────┬────────┘     └─────────────────┘
                                 │
                                 ▼
                        ┌─────────────────┐
                        │    ChromaDB     │
                        │ (Vector Store)  │
                        └─────────────────┘
```

## Features

- **Conversational AI**: Natural language chat powered by Google Gemini API
- **Knowledge Base**: Store and retrieve information using ChromaDB vector database
- **Context-Aware Responses**: Uses RAG (Retrieval Augmented Generation) for relevant answers
- **Modern Web UI**: Clean dark-themed interface built with HTML/CSS/JS
- **RESTful API**: FastAPI backend with full CRUD operations
- **Dual LLM Support**: Use Gemini API (recommended) or local Ollama

## Quick Start

### Prerequisites

1. **Python 3.9+**
2. **Google Gemini API Key** - Get free from [Google AI Studio](https://aistudio.google.com/app/apikey)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/jarvis-ai-assistant.git
   cd jarvis-ai-assistant
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv

   # Windows
   venv\Scripts\activate

   # Linux/Mac
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment**
   ```bash
   cp .env.example .env
   ```

   Edit `.env` and add your Gemini API key:
   ```
   LLM_PROVIDER=gemini
   GEMINI_API_KEY=your_api_key_here
   GEMINI_MODEL=gemini-2.0-flash
   ```

### Running the Application

1. **Start the server**
   ```bash
   python -m uvicorn app.main:app --reload
   ```

2. **Open your browser** at `http://127.0.0.1:8000`

That's it! The first startup may take a moment to download the embedding model (~90MB).

## Alternative: Using Ollama (Local LLM)

If you prefer running a local LLM instead of using Gemini API:

1. **Install Ollama** from [ollama.ai](https://ollama.ai)

2. **Start Ollama and pull a model**
   ```bash
   ollama serve
   ollama pull llama3.2:1b
   ```

3. **Update `.env`**
   ```
   LLM_PROVIDER=ollama
   OLLAMA_BASE_URL=http://localhost:11434
   OLLAMA_MODEL=llama3.2:1b
   ```

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Web UI |
| GET | `/api/status` | System status |
| POST | `/api/chat` | Send message to Jarvis |
| POST | `/api/knowledge/add` | Add documents to knowledge base |
| GET | `/api/knowledge` | Get all knowledge |
| DELETE | `/api/knowledge/{id}` | Delete specific document |
| DELETE | `/api/knowledge` | Clear all knowledge |
| POST | `/api/knowledge/search` | Search knowledge base |

## Tech Stack

- **LLM**: Google Gemini API (or Ollama for local)
- **Vector Database**: ChromaDB
- **Backend**: FastAPI
- **Frontend**: HTML, CSS, JavaScript
- **Embeddings**: Sentence Transformers (all-MiniLM-L6-v2)

## Project Structure

```
jarvis-ai-assistant/
├── app/
│   ├── __init__.py
│   ├── config.py          # Configuration settings
│   ├── main.py            # FastAPI application + static file serving
│   ├── llm_service.py     # LLM interaction (Gemini/Ollama)
│   └── vector_store.py    # ChromaDB operations
├── frontend/
│   ├── index.html         # Main web UI
│   ├── styles.css         # Dark theme styling
│   └── app.js             # Frontend JavaScript
├── data/                  # ChromaDB persistence (auto-created)
├── requirements.txt       # Python dependencies
├── .env.example          # Environment template
├── .env                  # Your configuration (create this)
├── .gitignore
└── README.md
```

## Usage Examples

### Adding Knowledge via UI
1. Click "Knowledge Base" in the sidebar
2. Enter information in the text area
3. Click "Add to Knowledge Base"

### Adding Knowledge via API
```bash
curl -X POST http://localhost:8000/api/knowledge/add \
  -H "Content-Type: application/json" \
  -d '{"documents": ["Your company info here", "More facts here"]}'
```

### Chatting via API
```bash
curl -X POST http://localhost:8000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What do you know about our company?"}'
```

## Configuration

Edit `.env` file to customize:

| Variable | Default | Description |
|----------|---------|-------------|
| LLM_PROVIDER | gemini | LLM provider: "gemini" or "ollama" |
| GEMINI_API_KEY | - | Your Google Gemini API key |
| GEMINI_MODEL | gemini-2.0-flash | Gemini model to use |
| OLLAMA_BASE_URL | http://localhost:11434 | Ollama API URL (if using Ollama) |
| OLLAMA_MODEL | llama2 | Ollama model to use |
| CHROMA_PERSIST_DIR | ./data/chroma_db | ChromaDB storage path |

## Troubleshooting

### "API key not valid" error
- Get a new API key from [Google AI Studio](https://aistudio.google.com/app/apikey)
- Make sure the key is correctly copied to `.env`
- Restart the server after changing `.env`

### "Model not found" (404) error
- The model name may have changed. Try `gemini-2.0-flash` or `gemini-1.5-pro`
- List available models:
  ```
  curl "https://generativelanguage.googleapis.com/v1beta/models?key=YOUR_KEY"
  ```

### Embedding model download stuck
- First startup downloads ~90MB model. Be patient.
- If interrupted, delete the cache and restart:
  ```bash
  # Windows
  rmdir /s %USERPROFILE%\.cache\huggingface

  # Linux/Mac
  rm -rf ~/.cache/huggingface
  ```

## License

MIT License

## Contributing

Pull requests are welcome!
