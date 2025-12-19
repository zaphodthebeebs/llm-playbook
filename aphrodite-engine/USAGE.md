# Aphrodite Engine Usage

## OpenAI-Compatible API

Aphrodite provides the same OpenAI-compatible API as vLLM, plus extended sampling parameters.

### Chat Completion (Basic)

```bash
curl http://localhost:8083/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [
      {"role": "user", "content": "Write a short story about a dragon"}
    ],
    "temperature": 0.8,
    "max_tokens": 500
  }'
```

### Chat Completion (Advanced Sampling)

```bash
curl http://localhost:8083/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [
      {"role": "system", "content": "You are a creative storyteller."},
      {"role": "user", "content": "Tell me an epic fantasy tale"}
    ],
    "temperature": 0.9,
    "top_p": 0.95,
    "top_k": 40,
    "repetition_penalty": 1.15,
    "frequency_penalty": 0.1,
    "presence_penalty": 0.1,
    "min_p": 0.05,
    "typical_p": 0.95,
    "max_tokens": 1000
  }'
```

### Streaming

```bash
curl http://localhost:8083/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [{"role": "user", "content": "Write a poem"}],
    "stream": true,
    "temperature": 0.9
  }'
```

## Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8083/v1",
    api_key="dummy"
)

# Basic chat
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "You are a creative writing assistant."},
        {"role": "user", "content": "Write a short sci-fi story."}
    ],
    temperature=0.85,
    max_tokens=800
)
print(response.choices[0].message.content)

# Streaming
stream = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Tell me a story"}],
    stream=True,
    temperature=0.9
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end='', flush=True)
```

## Advanced Sampling Parameters

### Repetition Control

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Write a dialogue"}],
    repetition_penalty=1.15,  # Reduce repetition (1.0 = off, >1.0 = penalize)
    frequency_penalty=0.1,    # Penalize frequent tokens
    presence_penalty=0.1      # Penalize tokens that appeared
)
```

### Creative Sampling

```python
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Be creative!"}],
    temperature=0.95,    # Higher = more random
    top_p=0.95,         # Nucleus sampling
    min_p=0.05,         # Minimum probability threshold (Aphrodite)
    typical_p=0.95,     # Typical sampling (Aphrodite)
    top_k=40            # Top-k sampling
)
```

### Logit Bias

```python
# Encourage/discourage specific tokens
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Write something"}],
    logit_bias={
        "13": 5,   # Strongly encourage token 13
        "42": -5   # Strongly discourage token 42
    }
)
```

## Role-Playing / Character Chat

```python
# Character-based chat
response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "system", "content": "You are a wise wizard named Gandalf."},
        {"role": "user", "content": "What advice do you have for me?"}
    ],
    temperature=0.85,
    repetition_penalty=1.2,
    max_tokens=300
)
```

## List Models

```bash
curl http://localhost:8083/v1/models
```

## Check Model Info

```bash
curl http://localhost:8083/v1/models/meta-llama/Llama-3.1-70B-Instruct
```

## Monitor Performance

```bash
# Health check
curl http://localhost:8083/health

# View logs for timing info
docker logs -f aphrodite-engine
```

Look for:
- `Avg prompt throughput: X tokens/s`
- `Avg generation throughput: Y tokens/s`
- `GPU memory usage: Z GB`

## Sampling Parameter Guide

| Parameter | Range | Effect | Use Case |
|-----------|-------|--------|----------|
| `temperature` | 0.0-2.0 | Randomness | 0.7=balanced, 0.9=creative |
| `top_p` | 0.0-1.0 | Nucleus sampling | 0.95=good default |
| `top_k` | 1-100 | Limit choices | 40=good default |
| `repetition_penalty` | 1.0-1.5 | Reduce repeats | 1.1-1.2 for dialogue |
| `frequency_penalty` | -2.0-2.0 | Penalize frequency | 0.1=subtle, 1.0=strong |
| `presence_penalty` | -2.0-2.0 | Penalize presence | 0.1=encourage variety |
| `min_p` | 0.0-1.0 | Min probability | 0.05=filter unlikely |
| `typical_p` | 0.0-1.0 | Typical sampling | 0.95=natural flow |

## Presets for Different Use Cases

### Creative Writing
```python
{
    "temperature": 0.9,
    "top_p": 0.95,
    "repetition_penalty": 1.15,
    "min_p": 0.05
}
```

### Dialogue/Chat
```python
{
    "temperature": 0.85,
    "top_p": 0.9,
    "repetition_penalty": 1.2,
    "frequency_penalty": 0.2
}
```

### Factual/Precise
```python
{
    "temperature": 0.3,
    "top_p": 0.9,
    "repetition_penalty": 1.1
}
```

### Experimental/Wild
```python
{
    "temperature": 1.2,
    "top_p": 0.95,
    "top_k": 100,
    "min_p": 0.01
}
```

## Tips

- Start with `temperature=0.8` and `repetition_penalty=1.1`
- Use higher repetition penalty for dialogue (1.2-1.3)
- Combine `min_p` with `temperature` for controlled creativity
- Monitor logs to see actual tokens/sec performance
- For long conversations, increase `max_model_len` in config
