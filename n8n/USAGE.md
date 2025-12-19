# n8n Usage Guide

## Access Web Interface

```
http://localhost:5678
```

## Basic Workflow Creation

### 1. Create New Workflow

1. Click "+ Add Workflow"
2. Name your workflow
3. Start adding nodes

### 2. Add Nodes

1. Click "+" button
2. Search for node type
3. Configure parameters
4. Connect to other nodes

### 3. Execute & Test

1. Click "Execute Workflow"
2. View results for each node
3. Debug with execution data

### 4. Activate

1. Toggle "Active" switch
2. Workflow runs on triggers

## Example Workflows

### Simple Ollama Chat

**Nodes:**
1. Webhook Trigger
2. HTTP Request (Ollama)
3. Respond to Webhook

**Configuration:**

**Webhook Node:**
- HTTP Method: POST
- Path: `chat`

**HTTP Request Node:**
```json
{
  "method": "POST",
  "url": "http://ollama:11434/api/chat",
  "body": {
    "model": "llama3.1",
    "messages": [
      {
        "role": "user",
        "content": "{{$json.body.message}}"
      }
    ],
    "stream": false
  }
}
```

**Respond to Webhook Node:**
- Response: `{{$json.message.content}}`

**Test:**
```bash
curl -X POST http://localhost:5678/webhook/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello!"}'
```

### OpenAI-Compatible LLM (vLLM/LocalAI)

**Nodes:**
1. Webhook
2. OpenAI Chat Model
3. Respond to Webhook

**OpenAI Node Configuration:**
- Credentials:
  - API Key: `dummy`
  - Base URL: `http://vllm:8000/v1` (or LocalAI)
- Model: `meta-llama/Llama-3.1-70B-Instruct`
- Messages: `{{$json.body.message}}`

### RAG: Document Q&A

**Upload Document Workflow:**

1. **Webhook** - Receive document
2. **Code** - Split into chunks
3. **HTTP Request** - Generate embeddings (Ollama)
4. **Postgres** - Store chunks + embeddings

**Question Workflow:**

1. **Webhook** - Receive question
2. **HTTP Request** - Generate question embedding
3. **Postgres** - Search similar chunks (pgvector)
4. **Code** - Format context
5. **HTTP Request** - LLM with context
6. **Respond** - Return answer

### Scheduled Content Generation

1. **Schedule Trigger** - Daily at 9 AM
2. **Postgres** - Fetch topics
3. **Loop** - For each topic:
   - **HTTP Request** - Generate content (Ollama)
   - **HTTP Request** - Generate image (Stable Diffusion)
   - **Postgres** - Save content
   - **Webhook** - Post to CMS

## Working with LLMs

### Ollama API

**Chat:**
```javascript
// HTTP Request Node
{
  "method": "POST",
  "url": "http://ollama:11434/api/chat",
  "body": {
    "model": "llama3.1",
    "messages": [
      {"role": "system", "content": "You are helpful assistant"},
      {"role": "user", "content": "{{$json.question}}"}
    ]
  }
}
```

**Generate:**
```javascript
{
  "method": "POST",
  "url": "http://ollama:11434/api/generate",
  "body": {
    "model": "llama3.1",
    "prompt": "{{$json.prompt}}"
  }
}
```

**Embeddings:**
```javascript
{
  "method": "POST",
  "url": "http://ollama:11434/api/embeddings",
  "body": {
    "model": "llama3.1",
    "prompt": "{{$json.text}}"
  }
}
```

### vLLM (OpenAI Compatible)

Use the **OpenAI** node:
- Credential Base URL: `http://vllm:8000/v1`
- Model: Your loaded model name
- Works exactly like OpenAI

### LocalAI (Multi-Modal)

**Text:**
```javascript
{
  "method": "POST",
  "url": "http://localai:8082/v1/chat/completions",
  "body": {
    "model": "llama-3",
    "messages": [{"role": "user", "content": "Hello"}]
  }
}
```

**Image Generation:**
```javascript
{
  "method": "POST",
  "url": "http://localai:8082/v1/images/generations",
  "body": {
    "model": "stablediffusion",
    "prompt": "{{$json.prompt}}",
    "size": "512x512"
  }
}
```

### Stable Diffusion

```javascript
{
  "method": "POST",
  "url": "http://stable-diffusion:7860/sdapi/v1/txt2img",
  "body": {
    "prompt": "{{$json.prompt}}",
    "negative_prompt": "ugly, blurry",
    "steps": 20,
    "width": 512,
    "height": 512,
    "cfg_scale": 7
  }
}
```

## Code Node Examples

### JavaScript for Data Processing

```javascript
// Process LLM response
const response = $input.item.json;
const text = response.message.content;

// Extract information
const lines = text.split('\n');
const items = lines.map(line => ({
  text: line,
  length: line.length
}));

return items.map(item => ({json: item}));
```

### Python for Advanced Processing

```python
import json

# Access input data
data = items[0]['json']

# Process
result = {
    'processed': data['text'].upper(),
    'word_count': len(data['text'].split())
}

# Return
return [{'json': result}]
```

## Webhook Patterns

### Simple Webhook

```
URL: http://localhost:5678/webhook/your-path
Method: POST
```

Test:
```bash
curl -X POST http://localhost:5678/webhook/your-path \
  -H "Content-Type: application/json" \
  -d '{"data": "test"}'
```

### Authenticated Webhook

Add **Basic Auth** header:
```bash
curl -X POST http://localhost:5678/webhook/secure \
  -H "Authorization: Basic $(echo -n 'user:pass' | base64)" \
  -d '{"data": "test"}'
```

### Webhook with Custom Response

Use **Respond to Webhook** node:
- Status Code: 200
- Response Data: `{{$json.result}}`
- Headers: `{"Content-Type": "application/json"}`

## Credential Management

### Add OpenAI Credential (for vLLM/LocalAI)

1. Credentials → Add Credential
2. Select "OpenAI API"
3. API Key: `dummy`
4. Additional Fields → Base URL: `http://vllm:8000/v1`
5. Save

### Add PostgreSQL Credential

1. Select "Postgres"
2. Host: `postgres`
3. Database: `n8n`
4. User: `n8n`
5. Password: (from docker-compose)

### Environment Variables in Credentials

Set in docker-compose:
```yaml
environment:
  - OPENAI_API_KEY=your_key
```

Use in workflows:
```javascript
{{$env.OPENAI_API_KEY}}
```

## Loops and Conditionals

### Loop Over Items

1. Add **Split In Batches** node
2. Batch Size: 1
3. Add processing nodes
4. Connect back to Split In Batches for next iteration

### IF Conditional

1. Add **IF** node
2. Condition: `{{$json.score}} > 0.5`
3. Connect "true" and "false" paths

### Switch (Multiple Conditions)

1. Add **Switch** node
2. Mode: Rules
3. Add rules for each case

## Error Handling

### Try-Catch Pattern

1. Add **Error Trigger** node
2. Connects to main workflow
3. Handles errors from any node
4. Send alert, log, or retry

### Retry Logic

In node settings:
- Continue On Fail: Yes
- Retry On Fail: Yes
- Max Tries: 3
- Wait Between Tries: 1000ms

## Variables and Expressions

### Access Input Data

```javascript
{{$json.field_name}}           // Current item
{{$json.nested.field}}         // Nested field
{{$input.item.json.field}}     // Alternative syntax
```

### Access Previous Node Data

```javascript
{{$node["Node Name"].json.field}}
```

### Environment Variables

```javascript
{{$env.VARIABLE_NAME}}
```

### Functions

```javascript
{{$now}}                       // Current timestamp
{{$today}}                     // Today's date
{{$json.text.toUpperCase()}}  // String functions
{{Math.random()}}             // Math functions
```

## Execution Modes

### Manual Execution

Click "Execute Workflow" button - runs once for testing

### Trigger-Based

Workflow runs automatically when trigger fires:
- Webhook received
- Schedule reached
- File uploaded
- Database change

### Queue Mode

For production:
```yaml
environment:
  - EXECUTIONS_MODE=queue
```

Benefits:
- Concurrent execution
- Better resource management
- Execution history

## Monitoring

### View Executions

1. Click "Executions" in left menu
2. See all workflow runs
3. Click to view details
4. See data for each node

### Logs

```bash
# View n8n logs
docker logs -f n8n

# Filter for errors
docker logs n8n 2>&1 | grep ERROR
```

### Alerts

Create workflow:
1. **Error Trigger**
2. **Email** or **Slack** node
3. Send alert with error details

## Performance Tips

### Optimize Workflows

- Minimize data passed between nodes
- Use filters early to reduce items
- Batch process when possible
- Disable unnecessary data saving

### Caching

Store frequently used data:
- Use **Set** node to create variables
- Use **Sticky** node for cross-workflow data
- Cache embeddings in database

### Async Operations

Use webhooks for long-running tasks:
1. Webhook receives request
2. Return immediate response
3. Process asynchronously
4. Callback when done

## Community Templates

Browse and import:
1. Visit [n8n.io/workflows](https://n8n.io/workflows)
2. Search for "LLM", "OpenAI", "RAG"
3. Click "Use Workflow"
4. Copy JSON or import directly

Popular templates:
- AI chatbots
- Content generation
- Email automation with AI
- Document processing
- Image generation pipelines

## API Usage

### Trigger Workflow via API

```bash
# Get workflow ID from URL
WORKFLOW_ID=your_workflow_id

# Execute
curl -X POST http://localhost:5678/api/v1/workflows/$WORKFLOW_ID/execute \
  -H "Content-Type: application/json" \
  -d '{"data": "test"}'
```

### Create Workflow via API

```bash
curl -X POST http://localhost:5678/api/v1/workflows \
  -H "Content-Type: application/json" \
  -d @workflow.json
```

## Tips & Best Practices

1. **Name Nodes Clearly** - Helps with debugging
2. **Use Comments** - Document complex logic
3. **Test Incrementally** - Execute after each node
4. **Version Control** - Export workflows as JSON
5. **Error Handling** - Always add error triggers
6. **Monitor** - Check execution history regularly
7. **Optimize** - Remove unnecessary data passing
8. **Modular** - Break complex workflows into smaller ones

## Resources

- [n8n Documentation](https://docs.n8n.io/)
- [Expression Reference](https://docs.n8n.io/code-examples/expressions/)
- [Node Reference](https://docs.n8n.io/integrations/)
- [Community Forum](https://community.n8n.io/)
- [Workflow Templates](https://n8n.io/workflows)

## Next Steps

1. Build a simple chatbot with Ollama
2. Create a RAG pipeline
3. Automate content generation
4. Connect to external APIs
5. Build an AI agent workflow
