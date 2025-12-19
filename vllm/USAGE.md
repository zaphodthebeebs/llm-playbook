# vLLM Usage Guide

## Starting the Server

```bash
cd /workspace/llm/vllm
docker-compose up -d

# Check logs
docker logs -f vllm

# Wait for: "Uvicorn running on http://0.0.0.0:8000"
```

## Basic API Examples

### Check Available Models

```bash
curl http://localhost:8000/v1/models
```

### Simple Completion

```bash
curl http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "prompt": "The capital of France is",
    "max_tokens": 50
  }'
```

### Chat Completion

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Explain quantum computing briefly"}
    ],
    "temperature": 0.7,
    "max_tokens": 200
  }'
```

### Streaming Response

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [{"role": "user", "content": "Write a short poem"}],
    "stream": true
  }'
```

## Python SDK

### Using OpenAI Library

```python
from openai import OpenAI

# Point to vLLM server
client = OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="dummy"  # vLLM doesn't need auth
)

# Chat completion
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "You are a coding assistant"},
        {"role": "user", "content": "Write a Python function to reverse a string"}
    ],
    temperature=0.7,
    max_tokens=500
)

print(response.choices[0].message.content)
```

### Streaming in Python

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Tell me a story"}],
    stream=True
)

for chunk in response:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end='')
```

## Advanced Usage

### With Custom Parameters

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [{"role": "user", "content": "Creative writing prompt"}],
    "temperature": 0.9,
    "top_p": 0.95,
    "frequency_penalty": 0.5,
    "presence_penalty": 0.3,
    "max_tokens": 1000,
    "stop": ["\n\n", "THE END"]
  }'
```

### Multi-turn Conversation

```python
messages = [
    {"role": "system", "content": "You are a math tutor"}
]

# First question
messages.append({"role": "user", "content": "What is 25 * 4?"})
response = client.chat.completions.create(model="...", messages=messages)
messages.append({"role": "assistant", "content": response.choices[0].message.content})

# Follow-up question
messages.append({"role": "user", "content": "Now divide that by 5"})
response = client.chat.completions.create(model="...", messages=messages)
print(response.choices[0].message.content)
```

## Monitoring

### Health Check

```bash
curl http://localhost:8000/health
# Returns: {"status": "ok"}
```

### Metrics (Prometheus)

```bash
curl http://localhost:8000/metrics

# Key metrics:
# - vllm:num_requests_running
# - vllm:num_requests_waiting
# - vllm:gpu_cache_usage_perc
# - vllm:time_to_first_token_seconds
```

### GPU Usage

```bash
# Watch GPU in real-time
watch -n 1 nvidia-smi
```

## Performance Tips

### Batch Requests for Throughput

```python
import asyncio
from openai import AsyncOpenAI

client = AsyncOpenAI(base_url="http://localhost:8000/v1", api_key="dummy")

async def process_request(prompt):
    response = await client.chat.completions.create(
        model="meta-llama/Llama-3.1-70B-Instruct",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

# Process 10 requests concurrently
prompts = ["Question 1", "Question 2", ...]
results = await asyncio.gather(*[process_request(p) for p in prompts])
```

### Optimize for Latency

Use shorter contexts and lower temperature:

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Quick answer: What is AI?"}],
    max_tokens=100,  # Shorter response
    temperature=0.3  # More deterministic
)
```

## Common Use Cases

### Code Generation

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "You are an expert programmer"},
        {"role": "user", "content": "Write a Python function to check if a number is prime"}
    ],
    temperature=0.2,  # Low temperature for code
    max_tokens=500
)
```

### Creative Writing

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "You are a creative fiction writer"},
        {"role": "user", "content": "Write the opening paragraph of a sci-fi novel"}
    ],
    temperature=0.9,  # High temperature for creativity
    max_tokens=800
)
```

### Data Extraction

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "Extract structured data in JSON format"},
        {"role": "user", "content": "Extract name, email, and phone from: John Doe, john@example.com, 555-1234"}
    ],
    temperature=0.1,  # Very low for structured output
    max_tokens=200
)
```

## Troubleshooting

### Server Won't Start

```bash
# Check logs
docker logs vllm

# Common issues:
# - Model downloading (wait 5-15 minutes for first run)
# - Out of memory (try smaller model or quantized)
# - CUDA errors (check nvidia-smi)
```

### Slow Responses

```bash
# Check if GPU is utilized
nvidia-smi

# If GPU usage is low:
# - Increase batch size in requests
# - Check network latency
# - Try different model
```

### Connection Refused

```bash
# Check if container is running
docker ps | grep vllm

# Check if port is open
netstat -tulpn | grep 8000

# Restart container
docker-compose restart
```

## Stopping the Server

```bash
# Stop
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

## Next Steps

- Try different models (see docker-compose.yml comments)
- Integrate with your application
- Set up monitoring dashboard
- Read [vLLM documentation](https://docs.vllm.ai/) for advanced features
