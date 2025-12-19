# LocalAI Usage

## OpenAI-Compatible API

LocalAI is a drop-in replacement for OpenAI API.

### Chat Completion

```bash
curl http://localhost:8082/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-3",
    "messages": [{"role": "user", "content": "Hello!"}],
    "temperature": 0.7
  }'
```

### Completion

```bash
curl http://localhost:8082/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-3",
    "prompt": "The meaning of life is",
    "max_tokens": 100
  }'
```

### Embeddings

```bash
curl http://localhost:8082/v1/embeddings \
  -H "Content-Type: application/json" \
  -d '{
    "model": "all-minilm-l6-v2",
    "input": "Text to embed"
  }'
```

### Image Generation

```bash
curl http://localhost:8082/v1/images/generations \
  -H "Content-Type: application/json" \
  -d '{
    "model": "stablediffusion",
    "prompt": "A cyberpunk city at night",
    "size": "512x512"
  }'
```

### Audio Transcription (Whisper)

```bash
curl http://localhost:8082/v1/audio/transcriptions \
  -F file="@audio.mp3" \
  -F model="whisper-1"
```

## Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8082/v1",
    api_key="dummy"
)

# Chat
response = client.chat.completions.create(
    model="llama-3",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)

# Embeddings
embedding = client.embeddings.create(
    model="all-minilm-l6-v2",
    input="Text to embed"
)
print(embedding.data[0].embedding)

# Images
image = client.images.generate(
    model="stablediffusion",
    prompt="A beautiful landscape"
)
print(image.data[0].url)
```

## Model Management

### List Models

```bash
curl http://localhost:8082/v1/models
```

### Install from Gallery

```bash
curl http://localhost:8082/models/apply \
  -H "Content-Type: application/json" \
  -d '{"id": "gpt-4"}'
```

### Available Gallery Models

- `gpt-3.5-turbo` → Llama-based
- `gpt-4` → Mixtral-based
- `llama-3.1-8b-instruct`
- `mistral-7b-instruct`
- `phi-3-mini`

## Advanced Configuration

### Model YAML

```yaml
name: my-model
parameters:
  model: model.gguf
  temperature: 0.8
  top_k: 40
  top_p: 0.9
  context_size: 8192
  threads: 8
  f16: true
  gpu_layers: 35
template:
  chat: |
    <|im_start|>system
    {{.SystemPrompt}}<|im_end|>
    <|im_start|>user
    {{.Input}}<|im_end|>
    <|im_start|>assistant
stopwords: ["<|im_end|>"]
```

## Monitoring

```bash
# Health
curl http://localhost:8082/readyz

# Logs
docker logs -f localai
```

## Tips

- Use model gallery for easy setup
- Embeddings great for RAG
- Can run multiple models
- Add models without restart
