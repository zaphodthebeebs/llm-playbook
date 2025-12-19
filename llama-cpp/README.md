# llama.cpp - CPU/GPU Inference Engine

Efficient LLM inference in pure C/C++ with minimal dependencies.

## What is llama.cpp?

Originally created by Georgi Gerganov for running LLaMA models on MacBooks, llama.cpp has evolved into the most portable and efficient LLM inference engine supporting both CPU and GPU acceleration.

## Key Features

- **GGUF Format** - Quantized models (Q4, Q5, Q8)
- **CPU + GPU Offloading** - Mix CPU/GPU layers
- **Low Memory** - 4-bit quantization fits large models in RAM
- **Cross-platform** - Linux, macOS, Windows, even mobile
- **OpenAI API** - Compatible server mode
- **No Python** - Pure C/C++, minimal dependencies

## How It Works

```
GGUF Model (Quantized)
  ↓
Load into RAM
  ↓
Offload N layers to GPU (configurable)
  ↓
Inference (CPU + CUDA/Metal)
  ↓
OpenAI-compatible API Server
```

## GGUF Quantization Levels

| Quant | Size (70B) | Quality | Speed |
|-------|------------|---------|-------|
| Q2_K | ~28GB | ⭐⭐ | ⚡⚡⚡⚡⚡ |
| Q4_K_M | ~40GB | ⭐⭐⭐⭐ | ⚡⚡⚡⚡ |
| Q5_K_M | ~48GB | ⭐⭐⭐⭐⭐ | ⚡⚡⚡ |
| Q8_0 | ~70GB | ⭐⭐⭐⭐⭐ | ⚡⚡ |

**Recommended: Q4_K_M** - Best quality/size ratio

## Best For

✅ GGUF quantized models
✅ Limited VRAM (can use RAM)
✅ Apple Silicon (Metal)
✅ Experimentation & flexibility
✅ Offline/air-gapped systems

❌ Maximum throughput (use vLLM)
❌ Multi-GPU tensor parallelism

## Performance

**70B Q4_K_M on RTX 4090:**
- Throughput: ~15 tokens/sec
- Memory: 18GB VRAM + 22GB RAM (partial offload)
- Latency: 80ms first token

## Quick Start

```bash
docker-compose up -d
curl http://localhost:8081/v1/models
```

## Where to Get GGUF Models

- [TheBloke on HuggingFace](https://huggingface.co/TheBloke) - 1000+ models
- [bartowski](https://huggingface.co/bartowski) - Latest models
- [mradermacher](https://huggingface.co/mradermacher) - Quality quants

Example: `TheBloke/Llama-2-70B-Chat-GGUF`

## vs Other Engines

| Feature | llama.cpp | vLLM | Ollama |
|---------|-----------|------|--------|
| Format | GGUF | HF | GGUF |
| CPU/GPU Mix | ✅ | ❌ | ✅ |
| Ease of use | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Speed | ⚡⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ |
| Flexibility | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
