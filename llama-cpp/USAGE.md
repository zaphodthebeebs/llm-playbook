# llama.cpp Usage

## OpenAI-Compatible API

llama.cpp server provides OpenAI-compatible endpoints.

### Chat Completion

```bash
curl http://localhost:8081/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-2-70b-chat",
    "messages": [
      {"role": "user", "content": "Explain neural networks"}
    ],
    "temperature": 0.7,
    "max_tokens": 200
  }'
```

### Completion

```bash
curl http://localhost:8081/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-2-70b-chat",
    "prompt": "Once upon a time",
    "max_tokens": 100
  }'
```

### Streaming

```bash
curl http://localhost:8081/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-2-70b-chat",
    "messages": [{"role": "user", "content": "Tell me a story"}],
    "stream": true
  }'
```

## Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8081/v1",
    api_key="dummy"
)

response = client.chat.completions.create(
    model="llama-2-70b-chat",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

## Advanced Parameters

```json
{
  "temperature": 0.7,      // 0.0-2.0
  "top_p": 0.95,          // Nucleus sampling
  "top_k": 40,            // Top-k sampling
  "repeat_penalty": 1.1,  // Repetition penalty
  "n_predict": 128,       // Max tokens (llama.cpp specific)
  "stop": ["</s>", "\n\n"] // Stop sequences
}
```

## Monitor Performance

Check server logs:
```bash
docker logs -f llama-cpp
```

Look for:
- `llama_print_timings` - Token speed
- `prompt eval time` - How long to process prompt
- `eval time` - Generation speed

## Adjust GPU Layers

If running out of VRAM or want faster:

```bash
# Edit docker-compose.yml
- N_GPU_LAYERS=41  # Reduce for less VRAM usage

# Restart
docker-compose restart
```

## Tips

- **Faster inference:** Increase GPU layers
- **Less VRAM:** Decrease GPU layers (uses RAM)
- **Longer context:** Increase CTX_SIZE
- **Better quality:** Use Q5 or Q8 quantization

## Health Check

```bash
curl http://localhost:8081/health
```
