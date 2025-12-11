# SmartSpec AI (SpecTacular.AI)

> Intelligent, offline AI system for automated test case generation from SRS documents

![Python](https://img.shields.io/badge/Python-3.8+-blue)
![Flask](https://img.shields.io/badge/Flask-3.0+-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

## 🎯 Overview

SmartSpec AI is a powerful, web-based AI system that automatically generates comprehensive test cases from Software Requirements Specification (SRS) documents. It combines advanced Transformer models, semantic search, and intelligent validation to produce high-quality, traceable test cases—all while running completely offline.

**Key Features:**
- 🤖 AI-powered test case generation using Transformer models
- 🔍 Semantic search with FAISS for intelligent requirement matching
- ✅ Automated validation and scoring of test cases
- 📊 Traceability matrix generation (Excel/PDF)
- 🔐 100% offline operation—no cloud dependencies
- 💾 File-based persistence with automatic organization
- 🎨 Interactive web interface with real-time feedback

## 🚀 Quick Start

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)
- ~2GB available disk space (for models and data)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/SpecTacularAI.git
   cd SpecTacularAI
   ```

2. **Create a virtual environment** (recommended)
   ```bash
   python -m venv venv
   
   # On Windows
   venv\Scripts\activate
   
   # On macOS/Linux
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application**
   ```bash
   python app.py
   ```

5. **Access the web interface**
   - Open your browser and navigate to `http://localhost:5000`

### Usage

1. **Upload an SRS Document**
   - Click "Upload SRS" and select a PDF file
   - The system extracts and indexes the requirements automatically

2. **Generate Test Cases**
   - Enter your test requirements or queries
   - Click "Generate Test Cases"
   - Review the AI-generated test cases

3. **Validate Results**
   - Review the validation scoring and feedback
   - Adjust requirements if needed for better results

4. **Export Results**
   - Export test cases as PDF or Excel
   - Download traceability matrix
   - Save validation reports

## System Architecture

### Core Architecture Pattern
The application follows a modular Flask-based architecture with clear separation of concerns:
- **Frontend**: Single-page web application using Bootstrap and vanilla JavaScript
- **Backend**: Flask REST API with modular Python components
- **AI Engine**: Transformer-based encoder-decoder architecture with FAISS semantic search
- **Processing Pipeline**: Document → Semantic Search → Test Case Generation → Validation → Export

### Key Design Decisions
1. **Offline-First**: No external API dependencies - all AI processing runs locally
2. **Modular Design**: Each major function (preprocessing, semantic search, validation, etc.) is isolated in separate modules
3. **File-Based Storage**: Uses local file system with date-based organization instead of database
4. **Transformer Architecture**: Custom encoder-decoder implementation alongside SentenceTransformers for semantic search

## Key Components

### Backend Components (`src/` directory)
- **preprocessing.py**: PDF text extraction and chunking using PyPDF2
- **semantic_search.py**: FAISS-based similarity search with SentenceTransformers embeddings
- **encoder.py/decoder.py**: Custom Transformer encoder-decoder models with positional encoding
- **validation_engine.py**: Rule-based test case validation with scoring system
- **mapping_engine.py**: Requirement-to-test-case mapping with pattern matching
- **traceability_matrix.py**: Excel-based traceability matrix generation
- **pdf_generator.py**: ReportLab-based PDF export functionality
- **vocabulary.py**: Custom vocabulary management for Transformer models
- **training.py**: Training utilities for the Transformer models
- **interactive_qa.py**: CLI interface for query-based test case generation

### Frontend Components (`web/` directory)
- **index.html**: Main SPA interface with Bootstrap UI components
- **static/css/styles.css**: Custom styling with CSS variables and enhanced UX
- **static/js/app.js**: JavaScript application logic with file upload, API calls, and UI management

### Main Application Files
- **app.py**: Flask application with all API endpoints and global state management
- **main.py**: Application entry point

## Data Flow

1. **Document Upload**: PDF files uploaded via web interface, stored in date-based folders
2. **Text Processing**: PDF text extraction → cleaning → chunking into 200-word segments
3. **Semantic Indexing**: Text chunks embedded using SentenceTransformers → FAISS index creation
4. **Query Processing**: User queries → semantic search → context retrieval
5. **Test Case Generation**: Context + query → Transformer decoder → structured test cases
6. **Validation**: Generated test cases → rule-based validation → scoring and feedback
7. **Mapping**: Requirements extraction → test case mapping → coverage analysis
8. **Export**: Multiple formats (PDF, Excel) for test cases, validation reports, and traceability matrices

## External Dependencies

### Core Libraries
- **Flask**: Web framework with CORS support
- **PyTorch**: Deep learning framework for Transformer models
- **SentenceTransformers**: Pre-trained embedding models (all-MiniLM-L6-v2)
- **FAISS**: Vector similarity search (CPU version for offline operation)
- **PyPDF2**: PDF text extraction
- **ReportLab**: PDF generation
- **OpenPyXL**: Excel file generation
- **Bootstrap 5**: Frontend UI framework
- **Font Awesome**: Icon library

### Model Dependencies
- Uses `all-MiniLM-L6-v2` from SentenceTransformers for semantic embeddings
- Custom Transformer encoder-decoder models built with PyTorch
- No cloud API dependencies - fully self-contained

## Deployment Strategy

### Local Development
- Flask development server on port 5000
- File-based storage in `data/` directory with date-based organization
- Static files served from `web/` directory
- Environment variables for configuration (SESSION_SECRET)

### Production Considerations
- Designed for offline deployment scenarios
- No database required - uses file system for persistence
- All AI models can be pre-downloaded and cached locally
- Session management with configurable secret keys
- Date-based file organization for easy maintenance

### File Structure
```
data/
├── YYYY-MM-DD/
│   ├── uploaded_srs.pdf
│   └── uploaded_testcases.json
web/
├── index.html
├── static/
│   ├── css/styles.css
│   └── js/app.js
src/
├── [AI and processing modules]
```

### Scalability Notes
- FAISS indexing scales well with document size
- Chunk-based processing allows handling large documents
- Modular architecture enables easy feature additions
- Stateless API design supports horizontal scaling if needed

The system is specifically designed for environments requiring complete offline operation, such as secure enterprise environments or locations with limited internet connectivity.

## 🌐 Deployment

### Recommended: Railway.app ⭐

Railway is perfect for Python/ML projects - no size limits, easy setup.

1. **Go to https://railway.app** and sign up with GitHub
2. **Create new project** → Import from GitHub repository
3. **Set environment variables:**
   - `FLASK_ENV`: `production`
   - `SESSION_SECRET`: [generate a secure key]
4. **Deploy** - Automatic on every git push

### Alternative Platforms
- **Render.com** - Good Flask support
- **Docker** - Full local/cloud control
- **AWS/GCP** - Enterprise deployments

See [DEPLOYMENT.md](DEPLOYMENT.md) for detailed guides on all platforms.

## 📦 Project Structure

```
SpecTacularAI/
├── app.py                 # Flask application entry point
├── main.py               # CLI entry point
├── requirements.txt      # Python dependencies
├── readme.md            # This file
├── .gitignore           # Git ignore rules
├── vercel.json          # Vercel deployment config
├── src/
│   ├── preprocessing.py        # PDF processing
│   ├── semantic_search.py       # FAISS indexing
│   ├── encoder.py/decoder.py   # Transformer models
│   ├── validation_engine.py     # Test case validation
│   ├── mapping_engine.py        # Requirement mapping
│   ├── traceability_matrix.py   # Matrix generation
│   ├── pdf_generator.py         # PDF export
│   ├── training.py              # Model training
│   ├── interactive_qa.py        # CLI interface
│   └── vocabulary.py            # Vocab management
├── web/
│   ├── index.html
│   └── static/
│       ├── css/styles.css
│       └── js/app.js
└── data/
    ├── YYYY-MM-DD/             # Date-organized files
    └── exports/                # Generated exports
```

## 🛠️ Configuration

### Environment Variables
- `FLASK_ENV`: Set to `production` for production deployments
- `FLASK_APP`: Defaults to `app.py`
- `SESSION_SECRET`: Secure key for sessions (auto-generated in dev)

### Customization
- **Chunk Size**: Modify `chunk_size` in `src/preprocessing.py` (default: 200 words)
- **Model Selection**: Change embedding model in `src/semantic_search.py`
- **FAISS Parameters**: Tune similarity search in `src/semantic_search.py`

## 📊 Performance Tips

- **First Run**: Initial model download takes 5-10 minutes (cached thereafter)
- **Large Documents**: Files > 100 pages process in chunks for memory efficiency
- **Search Speed**: FAISS indexes enable sub-100ms semantic searches
- **Batch Processing**: Upload multiple SRS documents for comprehensive coverage

## 🧪 Testing

```bash
# Run tests
python -m pytest tests/

# Run with coverage
pytest --cov=src tests/
```

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see LICENSE file for details.

## 🆘 Troubleshooting

### Module not found errors
```bash
pip install -r requirements.txt --upgrade
```

### Port already in use
```bash
# Change port in app.py or use:
python -m flask run --port 5001
```

### Out of memory with large files
- Process documents in smaller batches
- Increase chunk size in preprocessing
- Run on a machine with more RAM

### Models not downloading
- Check internet connection
- Manually set cache directory:
  ```bash
  export HF_HOME=/path/to/cache
  python app.py
  ```

## 📧 Support

For issues, questions, or suggestions:
- Open an issue on GitHub
- Contact: your-email@example.com
- Documentation: https://spectacular-ai-docs.example.com

---

**Last Updated:** December 2025 | Built with ❤️ for quality QA automation
