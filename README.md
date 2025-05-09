# AWS FAQ Knowledge Base

This project crawls, processes, and vectorizes AWS FAQ content to create a searchable knowledge base using vector embeddings. It provides a flexible platform for querying AWS documentation through semantic search capabilities. The knowledge base integrates with Flowise to create a conversational AI assistant that can answer questions about AWS services.

## 🚀 Features

- **Automated Web Crawling**: Targets AWS FAQ pages to extract relevant documentation
- **Content Processing**: Parses HTML and segments content by headers for structured data
- **Vectorization**: Creates embeddings using OpenAI's text-embedding-ada-002 model
- **Vector Storage**: Saves embeddings to ChromaDB for efficient semantic search
- **Scalable Architecture**: Configurable crawling parameters and content extraction
- **LLM Integration**: Compatible with LangFlow/Flowise visual workflow systems
- **Conversational AI**: Build Q&A systems around AWS documentation

# AWS FAQ Knowledge Base Workflow
![image](https://github.com/user-attachments/assets/850f4660-763e-43e4-80e3-eb5932849b7e)

## 📋 Prerequisites

- Python 3.7+
- pip package manager
- OpenAI API key (or compatible API provider)
- ChromaDB instance (local or remote)
- LangFlow or Flowise (optional, for visual workflow integration)

## 🛠️ Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/aws-faq-knowledge-base.git
cd aws-faq-knowledge-base
```

2. Install dependencies:
```bash
pip install scrapy==2.11.0 langchain==0.0.350 unstructured==0.11.5 nltk==3.8.1 openai==1.5.0 tiktoken==0.5.2
```

## ⚙️ Configuration

### Web Crawler Settings

The Scrapy crawler (`aws.py`) can be configured by modifying:

- `max_pages`: Control the number of pages to crawl
- `custom_settings`: Adjust crawler behavior (concurrency, retry policy, etc.)
- `start_urls`: Change the starting point for crawling
- `allowed_domains`: Restrict the crawler to specific domains

### ChromaDB Connection

Update these variables in the script:

```python
openai_api_key = "your-openai-api-key"
cromadb_server_api = "http://your-chromadb-instance:4001/api/v1"
collection_name = "aws_db"
```

## 📊 Project Structure

```
aws-faq-knowledge-base/
├── aws.py                  # Scrapy crawler
├── aws-faq-crawler.ipynb   # Jupyter notebook with full pipeline
├── aws_data/               # Folder containing crawled HTML data
├── langflow/               # LangFlow workflow files (optional)
├── flowise/                # Flowise workflow files (optional)
└── README.md               # This file
```

## 🚶 Usage

### 1. Run the crawler:

```bash
scrapy runspider aws.py
```

This will download HTML content from AWS FAQ pages into the `aws_data/` directory.

### 2. Process and create embeddings:

You can run the notebook cells sequentially or use the Python scripts directly:

```python
# Process HTML content
from glob import glob
from langchain.text_splitter import HTMLHeaderTextSplitter

# Define headers to split by
headers_to_split_on = [
    ("h1", "Header 1"),
    ("h2", "Header 2"),
    ("h3", "Header 3"), 
    ("p", "Paragraph")
]

# Process HTML files
all_html_files = glob('aws_data/*.html')
html_splitter = HTMLHeaderTextSplitter(headers_to_split_on=headers_to_split_on)
aws_contents = []

for html_file in all_html_files:
    with open(html_file, encoding="utf8") as f:
        html_text = f.read()
    
    if html_text and len(html_text) > 0:
        html_header_splits = html_splitter.split_text(html_text)
        if html_header_splits and len(html_header_splits) > 0:
            aws_contents += html_header_splits

# Create embeddings and store in ChromaDB
# (See notebook for full implementation)
```

### 3. Query the knowledge base programmatically:

```python
# Example querying code (not included in original notebook)
from langchain.embeddings import OpenAIEmbeddings
import requests

# Setup embeddings model
embeddings_model = OpenAIEmbeddings(openai_api_key=openai_api_key, model="text-embedding-ada-002")

# Create embedding for query
query = "How does AWS Lambda pricing work?"
query_embedding = embeddings_model.embed_query(query)

# Query ChromaDB
response = requests.post(f"{cromadb_server_api}/collections/{collection_id}/query", json={
    "query_embeddings": [query_embedding],
    "n_results": 5
})

# Process and display results
results = response.json()
# Logic to display relevant AWS FAQ content
```

### 4. Set up visual workflow (LangFlow/Flowise):

1. Import the workflow components:
   - Conversational AI model (e.g., Google Gemini, OpenAI)
   - Embedding model for query vectorization
   - ChromaDB connector to your AWS FAQ database
   - Retrieval QA chain to connect the components
   - Memory component for conversation history

2. Configure connection credentials:
   - Set API keys for LLM and embedding models
   - Configure ChromaDB connection URL and collection name

3. Deploy your conversational AWS FAQ assistant

## 🔒 Security Notes

- Never commit API keys to Git repositories
- Consider using environment variables for sensitive credentials
- Implement proper access controls for your ChromaDB instance
- Ensure LLM providers have appropriate usage limits set

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 💡 Visual Workflow Integration

This project integrates with visual workflow tools to create conversational AI systems around AWS documentation. Below is an example workflow using LangFlow:



The workflow consists of:

1. **ChatGoogleGenerativeAI**: Handles the conversational interface
2. **GoogleGenerativeAI Embeddings**: Processes queries into vector representations
3. **Chroma**: Connects to the ChromaDB vector database containing AWS FAQ content
4. **Conversational Retrieval QA Chain**: Combines the retrieval system with conversation context
5. **Buffer Window Memory**: Maintains conversation history

### Setting Up the Workflow

1. Import the workflow into LangFlow or Flowise
2. Configure the connection credentials:
   - Set your Google AI API key for the AI components
   - Configure the ChromaDB URL (e.g., `http://192.168.1.4:8000`)
   - Set the collection name to match your ChromaDB collection (e.g., `aws_db`)
3. Adjust parameters like temperature, memory size, etc. as needed
4. Deploy the workflow to create your AWS FAQ assistant

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgements

- Scrapy, ChromaDB projects
- Flowise for the visual workflow tools
