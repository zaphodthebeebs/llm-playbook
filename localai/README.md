# LocalAI - OpenAI Drop-in Replacement

Self-hosted OpenAI-compatible API supporting multiple model backends.

## What is LocalAI?

LocalAI is a free, open-source OpenAI alternative that runs locally. It's a drop-in replacement REST API compatible with OpenAI API specifications for local inferencing.

## Key Features

- **OpenAI Compatible** - Direct replacement for OpenAI API
- **Multiple Backends** - llama.cpp, vLLM, Diffusers, Whisper
- **Text Generation** - LLMs via llama.cpp/vLLM
- **Image Generation** - Stable Diffusion integration
- **Audio** - Whisper (transcription), piper (TTS)
- **Embeddings** - Text embeddings for RAG
- **No GPU Required** - Can run CPU-only
- **Model Gallery** - One-command model downloads

## How It Works

```
OpenAI API Request
  ↓
LocalAI Gateway
  ↓
Route to Backend:
├── llama.cpp (Text)
├── Stable Diffusion (Images)
├── Whisper (Audio)
└── Sentence Transformers (Embeddings)
  ↓
Return OpenAI-format Response
```

## Supported Features

### Text Completion
- `/v1/completions`
- `/v1/chat/completions`
- Streaming support

### Image Generation
- `/v1/images/generations`
- Stable Diffusion models

### Audio
- `/v1/audio/transcriptions` (Whisper)
- `/v1/audio/speech` (TTS)

### Embeddings
- `/v1/embeddings`
- For RAG applications

## Best For

✅ OpenAI API replacement
✅ Multi-modal needs (text + image + audio)
✅ Easy model management
✅ RAG applications (embeddings)
✅ CPU-only environments

❌ Maximum LLM performance (use vLLM)
❌ Single-purpose LLM serving

## Quick Start

```bash
docker-compose up -d
curl http://localhost:8082/readyz
```

## Model Gallery

LocalAI has a built-in gallery:

```bash
# List available models
curl http://localhost:8082/models/available

# Install a model
curl http://localhost:8082/models/apply \
  -H "Content-Type: application/json" \
  -d '{"id": "gpt-3.5-turbo"}'
```

Pre-configured models:
- Llama 3.1
- Mistral
- Phi-3
- And many more...

## vs Other Solutions

| Feature | LocalAI | vLLM | Ollama |
|---------|---------|------|--------|
| OpenAI API | ✅ Native | ✅ Yes | ⚠️ Partial |
| Multi-modal | ✅ Yes | ❌ No | ⚠️ Limited |
| Embeddings | ✅ Yes | ❌ No | ✅ Yes |
| Images | ✅ Yes | ❌ No | ❌ No |
| Audio | ✅ Yes | ❌ No | ❌ No |
| LLM Speed | ⚡⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ |
| Ease of use | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
