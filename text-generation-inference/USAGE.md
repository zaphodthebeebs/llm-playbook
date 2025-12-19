# TGI Usage Guide

## API Endpoints

- `/generate` - Single generation
- `/generate_stream` - Streaming generation
- `/health` - Health check
- `/info` - Model info
- `/metrics` - Prometheus metrics

## Basic Examples

### Generate Text

```bash
curl http://localhost:8080/generate \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": "Explain quantum computing:",
    "parameters": {
      "max_new_tokens": 200,
      "temperature": 0.7,
      "top_p": 0.95
    }
  }'
```

### Streaming

```bash
curl http://localhost:8080/generate_stream \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": "Write a poem about AI:",
    "parameters": {"max_new_tokens": 300}
  }'
```

### Model Info

```bash
curl http://localhost:8080/info
```

## Python Client

```python
import requests

url = "http://localhost:8080/generate"
data = {
    "inputs": "What is machine learning?",
    "parameters": {
        "max_new_tokens": 250,
        "temperature": 0.8,
        "do_sample": True
    }
}

response = requests.post(url, json=data)
print(response.json()["generated_text"])
```

## Streaming in Python

```python
response = requests.post(
    "http://localhost:8080/generate_stream",
    json={"inputs": "Tell me a story", "parameters": {"max_new_tokens": 500}},
    stream=True
)

for line in response.iter_lines():
    if line:
        print(line.decode())
```

## Parameters

```json
{
  "max_new_tokens": 100,      // Max tokens to generate
  "temperature": 0.7,          // 0.0-2.0, higher = more random
  "top_p": 0.95,               // Nucleus sampling
  "top_k": 50,                 // Top-k sampling
  "repetition_penalty": 1.2,   // Penalize repetition
  "do_sample": true,           // Enable sampling
  "seed": 42,                  // For reproducibility
  "stop": ["\n\n", "END"]      // Stop sequences
}
```

## Monitoring

```bash
# Prometheus metrics
curl http://localhost:8080/metrics

# Key metrics:
# - tgi_request_duration_seconds
# - tgi_request_count
# - tgi_queue_size
# - tgi_batch_current_size
```

## Health Check

```bash
curl http://localhost:8080/health
```

## Performance Tips

- Use `do_sample: false` for faster deterministic output
- Lower `max_new_tokens` for lower latency
- Batch requests for higher throughput
- Monitor `/metrics` to tune parameters
