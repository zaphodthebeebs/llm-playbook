# n8n - Workflow Automation Platform

Open-source workflow automation tool for connecting LLMs, APIs, and services.

## What is n8n?

n8n (pronounced "n-eight-n") is a fair-code licensed workflow automation tool. It's like Zapier or Make.com, but self-hosted and with deep integration capabilities for LLMs, databases, APIs, and hundreds of services.

## How It Works

```
Trigger (Webhook, Schedule, etc)
  ↓
n8n Workflow Engine
  ↓
Nodes (LLM, Database, API, etc)
  ↓
Data Processing & Logic
  ↓
Output (Webhook, Email, Database, etc)
```

## Key Features

### Workflow Automation
- **Visual Editor** - Drag-and-drop interface
- **280+ Integrations** - Pre-built nodes for services
- **Custom Code** - JavaScript/Python in workflows
- **Conditional Logic** - If/else, switches, loops
- **Error Handling** - Retry logic and fallbacks

### LLM Integration
- **OpenAI** - ChatGPT, GPT-4, embeddings
- **Anthropic** - Claude integration
- **HuggingFace** - Custom models
- **Local LLMs** - Connect to Ollama, vLLM, etc
- **Vector Databases** - Pinecone, Qdrant, Weaviate
- **RAG Pipelines** - Build retrieval augmented generation

### Developer Friendly
- **API Access** - RESTful API for workflows
- **Webhooks** - Trigger workflows from anywhere
- **Version Control** - Export/import workflows as JSON
- **Self-hosted** - Full data control
- **Postgres Backend** - Reliable data storage

### Use Cases
- **LLM Chatbots** - Build conversational agents
- **Data Processing** - ETL pipelines with LLMs
- **Content Generation** - Automated content workflows
- **AI Agents** - Multi-step autonomous workflows
- **RAG Systems** - Document Q&A systems
- **API Integration** - Connect LLMs to external services

## Architecture

```
┌─────────────────────────────────────┐
│         n8n Web UI (Port 5678)      │
├─────────────────────────────────────┤
│         Workflow Engine             │
│  ├── Triggers (Webhook, Schedule)   │
│  ├── Nodes (LLM, DB, API)           │
│  └── Logic (Conditional, Loop)      │
├─────────────────────────────────────┤
│      PostgreSQL Database            │
│  (Workflows, Credentials, Logs)     │
├─────────────────────────────────────┤
│      External Integrations          │
│  ├── LLMs (Ollama, OpenAI, etc)     │
│  ├── Databases (Postgres, Mongo)    │
│  ├── APIs (REST, GraphQL)           │
│  └── Services (Email, Slack, etc)   │
└─────────────────────────────────────┘
```

## Example Workflows

### 1. Chatbot with Ollama
```
Webhook (receive message)
  ↓
Ollama Chat Node (generate response)
  ↓
Webhook Response (send reply)
```

### 2. Document Q&A (RAG)
```
Webhook (upload document)
  ↓
Split Document into Chunks
  ↓
Generate Embeddings (Ollama)
  ↓
Store in Vector DB (Qdrant)
  ↓
---
Webhook (ask question)
  ↓
Generate Question Embedding
  ↓
Search Vector DB
  ↓
LLM Chat (answer with context)
  ↓
Return Answer
```

### 3. Content Generation Pipeline
```
Schedule Trigger (daily)
  ↓
Fetch Topics (Database/API)
  ↓
For Each Topic:
  ├── Generate Outline (LLM)
  ├── Generate Content (LLM)
  ├── Generate Image (Stable Diffusion)
  └── Post to CMS/Social Media
```

### 4. AI Agent
```
Webhook (receive task)
  ↓
LLM Planning (break down task)
  ↓
For Each Step:
  ├── Execute Action (API/Tool)
  ├── LLM Evaluation (check result)
  └── Adjust Plan if needed
  ↓
Return Final Result
```

## Integration with LLM Engines

n8n can connect to all your local LLM engines:

| Engine | Node Type | Connection |
|--------|-----------|------------|
| Ollama | HTTP Request | `http://ollama:11434` |
| vLLM | OpenAI Node | `http://vllm:8000/v1` |
| LocalAI | OpenAI Node | `http://localai:8082/v1` |
| KoboldCpp | HTTP Request | `http://koboldcpp:5001/v1` |

## Pre-built Nodes

### AI/LLM Nodes
- OpenAI (GPT-4, ChatGPT, DALL-E)
- Anthropic (Claude)
- HuggingFace
- Cohere
- Generic HTTP (for local LLMs)

### Vector Databases
- Pinecone
- Qdrant
- Weaviate
- Supabase
- Postgres with pgvector

### Data Processing
- Code (JavaScript/Python)
- Split/Merge
- Filter
- Sort
- Aggregate

### Communication
- Email (SMTP, Gmail)
- Slack
- Discord
- Telegram
- Webhook

### Storage
- PostgreSQL
- MySQL
- MongoDB
- Redis
- S3

## Performance

- **Lightweight** - ~200MB memory idle
- **Scalable** - Queue-based execution
- **Concurrent** - Multiple workflows simultaneously
- **Efficient** - Event-driven architecture

## Best For

✅ Building LLM-powered applications
✅ Automating workflows with AI
✅ RAG (Retrieval Augmented Generation)
✅ AI agent development
✅ Connecting LLMs to external services
✅ ETL with LLM processing
✅ Prototyping AI systems

❌ Simple one-off scripts (use Python)
❌ Real-time high-frequency trading
❌ Heavy computational tasks (use dedicated services)

## Example: Connect to Ollama

```javascript
// In n8n HTTP Request node
{
  "method": "POST",
  "url": "http://ollama:11434/api/chat",
  "body": {
    "model": "llama3.1",
    "messages": [
      {"role": "user", "content": "{{$json.question}}"}
    ]
  }
}
```

## Security Features

- **Credential Management** - Encrypted credential storage
- **Environment Variables** - Sensitive config via env vars
- **Webhook Authentication** - Secure webhook endpoints
- **SSL/TLS** - HTTPS support
- **Role-based Access** - User permissions (enterprise)

## Community

- **1000+ Workflows** - Community templates
- **Active Development** - Regular updates
- **Great Documentation** - Extensive guides
- **Forum Support** - Active community forum

## Why n8n for LLMs?

1. **Visual Development** - Build LLM workflows without code
2. **Rapid Prototyping** - Test LLM integrations quickly
3. **Production Ready** - Reliable execution engine
4. **Data Control** - Self-hosted, your data stays local
5. **Flexibility** - Connect to any LLM via HTTP
6. **Ecosystem** - 280+ integrations out of the box

## Pricing (Self-hosted)

- **Free** - Fair-code license
- **No Limits** - Unlimited workflows, executions
- **Full Features** - All functionality included
- **Community Support** - Free community help

*(Enterprise features like SSO, advanced permissions require paid license)*

## Getting Started

1. Start n8n (see INSTALL.md)
2. Open `http://localhost:5678`
3. Create account (first user is admin)
4. Create your first workflow
5. Add nodes and connect them
6. Test and activate!

## Popular Templates

Once installed, browse workflows at:
- n8n.io/workflows
- Community workflows
- LLM-specific templates
- RAG pipelines
- AI agent templates

Perfect for building AI-powered automation with your local LLM infrastructure!
