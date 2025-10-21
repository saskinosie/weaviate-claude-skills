# Weaviate Skills Collection

A comprehensive set of Claude Skills for working with Weaviate vector databases. These skills enable you to connect, manage, ingest data, and query Weaviate directly through Claude.ai or Claude Desktop.

## What are Claude Skills?

Claude Skills are self-contained knowledge modules that Claude can load on-demand. Each skill is a folder containing a `SKILL.md` file with instructions, code examples, and best practices for a specific task.

Skills provide:
- **Progressive Disclosure**: Claude only loads what it needs
- **Composability**: Skills can depend on other skills
- **Portability**: Easy to share and reuse across projects
- **No Deployment**: Works directly in Claude.ai web or Claude Desktop
- **Client-Friendly**: Just download folders and start using

## Available Skills

### 1. [weaviate-connection](weaviate-connection/)
Connect to Weaviate cloud or local instances with proper authentication and connection verification.

**When to use**: First step for any Weaviate work - establishes database connection

**Key features**:
- Cloud and local connection support
- Environment variable management
- Connection health checks
- Error troubleshooting

### 2. [weaviate-collection-manager](weaviate-collection-manager/)
Create, view, update, and delete Weaviate collections with comprehensive schema management.

**When to use**: Setting up database schema, managing collections

**Key features**:
- List and inspect collections
- Create collections with various vectorizers
- Configure RAG and reranking
- Data type reference tables
- Multi-modal collection support

### 3. [weaviate-data-ingestion](weaviate-data-ingestion/)
Upload data to Weaviate collections with support for single objects, batch uploads, and multi-modal content.

**When to use**: Adding documents, images, or any data to collections

**Key features**:
- Single and batch object insertion
- JSON/CSV file import
- Image upload (base64 encoding)
- Document chunking strategies
- Progress tracking for large uploads
- Error handling and validation

### 4. [weaviate-query-agent](weaviate-query-agent/)
Search and retrieve data using semantic search, filters, RAG, and hybrid queries.

**When to use**: Searching collections, Q&A, finding similar items

**Key features**:
- Semantic vector search
- Keyword (BM25) search
- Hybrid search
- Advanced filtering
- RAG (Retrieval Augmented Generation)
- Multi-modal image search
- Aggregation and analytics

## Quick Start

### Option 1: Claude.ai Web (Easiest)

1. Download this repository as ZIP
2. Extract the `weaviate-skills` folder
3. Open [Claude.ai](https://claude.ai)
4. In your conversation, say: "I have Weaviate skills in a folder. Let me share them."
5. Reference the skills folder when needed

### Option 2: Claude Desktop

1. Clone or download this repository
2. Place `weaviate-skills` in a known location (e.g., `~/Documents/`)
3. In Claude Desktop, reference skills by folder path
4. Example: "Load the skills from ~/Documents/weaviate-skills/"

### Option 3: Project Integration

1. Add `weaviate-skills/` to your project repository
2. Commit skills alongside your code
3. Claude will discover and use them automatically when working on your project

## Installation

### Prerequisites

```bash
# Install Python dependencies
pip install weaviate-client openai python-dotenv

# Or use requirements.txt
pip install -r requirements.txt
```

Create `requirements.txt`:
```
weaviate-client>=4.0.0
openai>=1.0.0
python-dotenv>=1.0.0
```

### Environment Setup

Your Weaviate credentials go in a `.env` file in this directory:

1. **Copy the template**:
   ```bash
   cp .env.example .env
   ```

2. **Edit `.env` with your credentials**:
   ```bash
   # Weaviate Connection (REQUIRED)
   WEAVIATE_URL=your-cluster.weaviate.cloud  # NO https:// prefix
   WEAVIATE_API_KEY=your-weaviate-api-key

   # Vectorizer API Keys (optional, only if using these services)
   OPENAI_API_KEY=your-openai-api-key
   COHERE_API_KEY=your-cohere-api-key
   ```

3. **Where to find your Weaviate credentials**:
   - Log into [Weaviate Cloud Console](https://console.weaviate.cloud/)
   - Click on your cluster
   - **REST Endpoint**: Copy the URL (without `https://`)
   - **API Key**: Click "API Keys" to view/generate

**Important**:
- `.env` is in `.gitignore` - never commit secrets to version control!
- Share `.env.example` with your clients as a template
- Each person needs their own `.env` file with their credentials

## Workflow

```
1. Connect to Weaviate (weaviate-connection)
   ↓
2. Create/Manage Collections (weaviate-collection-manager)
   ↓
3. Upload Data (weaviate-data-ingestion)
   ↓
4. Query and Search (weaviate-query-agent)
```

## Example Usage

### Example 1: Complete Setup

```
You: "I want to create a vector database for my technical documentation"

Claude: *Loads weaviate-connection skill*
        "Let me help you connect to Weaviate. Do you have a Weaviate Cloud
        account or a local instance?"

You: "I have a cloud instance at xyz.weaviate.cloud"

Claude: *Guides through connection setup*
        *Tests connection*
        ✅ Connected to Weaviate successfully!

You: "Create a collection for technical documents with RAG enabled"

Claude: *Loads weaviate-collection-manager skill*
        *Creates optimized collection schema*
        ✅ Created 'TechnicalDocuments' collection with OpenAI vectorizer and GPT-4 RAG

You: "Upload these 100 PDF files from /docs folder"

Claude: *Loads weaviate-data-ingestion skill*
        *Processes PDFs, chunks text, batch uploads*
        📊 Processing: [████████████████████] 100/100
        ✅ Successfully uploaded 100 documents (523 chunks)

You: "Find information about seismic zone requirements"

Claude: *Loads weaviate-query-agent skill*
        *Performs semantic search with RAG*

        🤖 Answer: Seismic zone requirements vary by location...
        📚 Sources: Building Code Manual (Page 45), Seismic Guidelines (Page 12)
```

### Example 2: Quick Search

```
You: "Search my product catalog for items similar to 'wireless headphones under $200'"

Claude: *Loads weaviate-connection and weaviate-query-agent*
        *Connects to existing collection*
        *Runs hybrid search with price filter*

        Found 8 products:
        • Sony WH-1000XM4 - $179.99
        • Bose QuietComfort 45 - $199.00
        • ...
```

### Example 3: Data Import

```
You: "Import products.json into my ProductCatalog collection"

Claude: *Loads weaviate-data-ingestion skill*
        *Validates JSON structure*
        *Batch uploads with progress tracking*

        📄 Loaded 500 products from products.json
        ✅ Successfully uploaded: 500
        ⏱️  Time: 12.3 seconds
```

## Skill Dependencies

Each skill declares its dependencies in the frontmatter:

```yaml
dependencies:
  - weaviate-connection
  - weaviate-collection-manager
```

Claude will automatically load prerequisite skills as needed.

## Sharing with Clients

### For Testing/Demos

1. Share the `weaviate-skills` folder via:
   - Email (ZIP file)
   - Dropbox/Google Drive link
   - GitHub repository

2. Provide your client with:
   - The skills folder
   - Their Weaviate credentials (`.env` template)
   - Simple instructions: "Open Claude.ai and tell it you have Weaviate skills to load"

### For Production Use

1. Create a dedicated Weaviate cluster for the client
2. Provide read-only or scoped API keys
3. Share the skills folder
4. Client can use Claude.ai web (no technical setup required)

## Best Practices

1. **Start with Connection**: Always load `weaviate-connection` first
2. **Check Schema**: Review collection schema before ingesting data
3. **Batch Upload**: Use batch operations for multiple objects
4. **Use Filters**: Combine vector search with filters for precision
5. **Monitor Quotas**: Track API usage for OpenAI/Cohere vectorizers
6. **Version Control**: Keep skills in your repository for team collaboration

## Troubleshooting

### "Cannot connect to Weaviate"
- Verify URL doesn't include `https://` prefix
- Check API key is correct
- Ensure cluster is running (for self-hosted)

### "No results found"
- Confirm collection has data
- Try broader search terms
- Check vectorizer is configured correctly

### "Batch upload fails"
- Reduce batch size
- Validate data types match schema
- Check for network timeouts

See individual skill files for detailed troubleshooting guides.

## Advanced Features

### Custom RAG Pipelines
Combine `weaviate-query-agent` with custom LLM calls for specialized Q&A systems.

### Multi-Modal Search
Use `weaviate-collection-manager` to create CLIP-enabled collections for image+text search.

### Automated Workflows
Chain skills together for complete data processing pipelines.

## Requirements

**Minimum**:
- Python 3.8+
- weaviate-client library
- Weaviate instance (cloud or local)

**Optional** (depending on vectorizer):
- OpenAI API key (for text2vec-openai)
- Cohere API key (for text2vec-cohere)
- HuggingFace models (for text2vec-huggingface)

## Project Structure

```
weaviate-skills/
├── README.md                          # This file
├── weaviate-connection/
│   └── SKILL.md                       # Connection skill
├── weaviate-collection-manager/
│   └── SKILL.md                       # Collection management skill
├── weaviate-data-ingestion/
│   └── SKILL.md                       # Data upload skill
└── weaviate-query-agent/
    └── SKILL.md                       # Query and search skill
```

## Contributing

We welcome contributions! To add a new skill:

1. Create a new folder: `skill-name/`
2. Add `SKILL.md` with YAML frontmatter
3. Include clear examples and error handling
4. Update this README
5. Submit a pull request

## Support

- [Weaviate Documentation](https://weaviate.io/developers/weaviate)
- [Weaviate Python Client](https://weaviate.io/developers/weaviate/client-libraries/python)
- [Claude Skills Guide](https://docs.anthropic.com/claude/docs/claude-skills)

## License

MIT License - Free to use, modify, and distribute.

---

**Built with ❤️ for the Weaviate community**

*Questions? Issues? Open an issue or reach out to the Weaviate community on [Discord](https://weaviate.io/slack).*