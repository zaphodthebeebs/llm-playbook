# n8n Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/n8n
docker-compose up -d
```

## Verify

```bash
# Check service is running
curl http://localhost:5678

# Check logs
docker logs -f n8n
```

## Access Web UI

Open browser to:
```
http://localhost:5678
```

## First-Time Setup

1. Open `http://localhost:5678`
2. Create your account (first user becomes admin)
3. Set email and password
4. Complete onboarding wizard

## Configuration

### Environment Variables

Edit `docker-compose.yml` to configure:

```yaml
environment:
  # Timezone
  - TZ=America/New_York

  # Webhook URL (for external access)
  - WEBHOOK_URL=https://your-domain.com

  # Security
  - N8N_BASIC_AUTH_ACTIVE=true
  - N8N_BASIC_AUTH_USER=admin
  - N8N_BASIC_AUTH_PASSWORD=your_password

  # Execution mode
  - EXECUTIONS_MODE=queue  # or 'regular'

  # Log level
  - N8N_LOG_LEVEL=info  # debug, info, warn, error
```

### Database Configuration

n8n uses PostgreSQL for:
- Workflow storage
- Credential storage
- Execution history
- Logs

Database is automatically created by docker-compose.

### Persistent Data

Data is stored in:
```
/workspace/llm/n8n/data/
  ├── workflows/     # Workflow definitions
  ├── credentials/   # Encrypted credentials
  └── database/      # PostgreSQL data
```

## Connecting to Local LLMs

### Add Ollama Connection

1. Create new workflow
2. Add "HTTP Request" node
3. Configure:
   - Method: POST
   - URL: `http://ollama:11434/api/chat`
   - Body (JSON):
     ```json
     {
       "model": "llama3.1",
       "messages": [{"role": "user", "content": "Hello"}]
     }
     ```

### Add vLLM Connection (OpenAI-compatible)

1. Add "OpenAI" node
2. Configure credentials:
   - API Key: `dummy` (not used)
   - Base URL: `http://vllm:8000/v1`
3. Use like regular OpenAI node

### Add LocalAI Connection

1. Add "OpenAI" node
2. Configure credentials:
   - API Key: `dummy`
   - Base URL: `http://localai:8082/v1`
3. Select model and use

## Network Configuration

n8n runs in Docker network with access to:
- `ollama:11434` - Ollama service
- `vllm:8000` - vLLM service
- `localai:8082` - LocalAI service
- `koboldcpp:5001` - KoboldCpp service
- `postgres:5432` - PostgreSQL database

Add n8n to your LLM docker network:
```yaml
networks:
  - llm-network
```

## Import Workflows

### From File

1. Create workflow JSON file
2. In n8n, click "Import from File"
3. Select file and import

### From URL

1. Copy workflow URL from n8n.io/workflows
2. Click "Import from URL"
3. Paste and import

### Example Workflow

Save as `ollama-chat.json`:
```json
{
  "name": "Ollama Chat",
  "nodes": [
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "chat",
        "responseMode": "responseNode",
        "options": {}
      },
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "position": [250, 300]
    },
    {
      "parameters": {
        "method": "POST",
        "url": "http://ollama:11434/api/chat",
        "jsonParameters": true,
        "options": {},
        "bodyParametersJson": "={\n  \"model\": \"llama3.1\",\n  \"messages\": [{\"role\": \"user\", \"content\": \"{{$json.body.message}}\"}],\n  \"stream\": false\n}"
      },
      "name": "Ollama",
      "type": "n8n-nodes-base.httpRequest",
      "position": [450, 300]
    },
    {
      "parameters": {
        "respondWith": "json",
        "responseBody": "={{$json.message.content}}"
      },
      "name": "Respond to Webhook",
      "type": "n8n-nodes-base.respondToWebhook",
      "position": [650, 300]
    }
  ],
  "connections": {
    "Webhook": {
      "main": [[{"node": "Ollama", "type": "main", "index": 0}]]
    },
    "Ollama": {
      "main": [[{"node": "Respond to Webhook", "type": "main", "index": 0}]]
    }
  }
}
```

Import and activate this workflow.

## Credentials Setup

### For External APIs

1. Click "Credentials" in left menu
2. Click "Add Credential"
3. Select credential type (OpenAI, Anthropic, etc)
4. Enter API key
5. Save

### For Local LLMs

No credentials needed! Use HTTP Request nodes with direct URLs.

## Enable Community Nodes

Install additional nodes from npm:

1. Settings → Community Nodes
2. Enable community nodes
3. Search and install (e.g., `n8n-nodes-qdrant`)

Popular LLM-related nodes:
- `n8n-nodes-langchain` - LangChain integration
- `n8n-nodes-qdrant` - Qdrant vector DB
- `n8n-nodes-weaviate` - Weaviate vector DB

## Troubleshooting

### Can't Access Web UI

```bash
# Check container is running
docker ps | grep n8n

# Check logs
docker logs n8n

# Verify port mapping
curl http://localhost:5678
```

### Can't Connect to LLMs

```bash
# Test from n8n container
docker exec -it n8n curl http://ollama:11434

# If fails, check network
docker network inspect llm-network
```

### Database Connection Issues

```bash
# Check postgres is running
docker ps | grep postgres

# Check logs
docker logs n8n-postgres

# Verify connection
docker exec -it n8n-postgres psql -U n8n -d n8n
```

### Reset Admin Password

```bash
# Stop n8n
docker-compose down

# Start with env var
N8N_BASIC_AUTH_PASSWORD=newpassword docker-compose up -d
```

## Update n8n

```bash
# Pull latest image
docker-compose pull

# Restart
docker-compose up -d

# Check version
curl http://localhost:5678/api/v1/health | jq .version
```

## Backup

```bash
# Backup workflows and credentials
docker exec n8n n8n export:workflow --all --output=/data/backups/workflows.json

# Backup database
docker exec n8n-postgres pg_dump -U n8n n8n > backup.sql

# Or backup entire data directory
tar -czf n8n-backup.tar.gz /workspace/llm/n8n/data/
```

## Restore

```bash
# Restore workflows
docker exec n8n n8n import:workflow --input=/data/backups/workflows.json

# Restore database
docker exec -i n8n-postgres psql -U n8n n8n < backup.sql
```

## Performance Tuning

### For Heavy Workflows

```yaml
environment:
  # Increase max payload size (default 16MB)
  - N8N_PAYLOAD_SIZE_MAX=64

  # Queue mode for concurrent workflows
  - EXECUTIONS_MODE=queue

  # Increase workers
  - N8N_CONCURRENCY_PRODUCTION_LIMIT=10
```

### For Many Executions

```yaml
environment:
  # Auto-delete old executions
  - EXECUTIONS_DATA_PRUNE=true
  - EXECUTIONS_DATA_MAX_AGE=168  # 7 days in hours
```

## Security Best Practices

1. **Enable Basic Auth** for production
2. **Use Environment Variables** for secrets
3. **Regular Backups** of workflows and credentials
4. **HTTPS** for external access (use reverse proxy)
5. **Firewall** - Only expose necessary ports

## Resources

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Workflows](https://n8n.io/workflows)
- [Community Forum](https://community.n8n.io/)
- [GitHub](https://github.com/n8n-io/n8n)

## Next Steps

1. Create your first workflow
2. Connect to Ollama
3. Build a chatbot
4. Explore community templates
5. Build RAG pipeline
