# Text Generation Inference (TGI)

HuggingFace's production-ready inference solution for LLMs.

## What is TGI?

Text Generation Inference is HuggingFace's optimized inference server built for deploying LLMs at scale. Used in production by HuggingFace for their Inference API serving millions of requests daily.

## Key Features

- **Tensor Parallelism** - Multi-GPU support
- **Continuous Batching** - Efficient request handling
- **Quantization** - GPTQ, AWQ, bitsandbytes, EETQ
- **Flash Attention & Paged Attention** - Memory efficient
- **Safetensors** - Fast model loading
- **Streaming** - Token-by-token responses
- **Built-in Metrics** - Prometheus monitoring

## How It Works

```
Request → gRPC/REST API → Request Queue → Batch Scheduler
  ↓
Continuous Batching (dynamic batching of requests)
  ↓
Flash Attention 2 (memory-efficient attention)
  ↓
Token Generation → Stream to Client
```

## Supported Models

- LLaMA (1, 2, 3, 3.1)
- Mistral & Mixtral
- Qwen (1.5, 2, 2.5)
- Phi-3
- Gemma
- StarCoder
- BLOOM
- 100+ models on HuggingFace

## Best For

✅ Production deployment
✅ HuggingFace model ecosystem
✅ Docker/Kubernetes environments
✅ Built-in monitoring needs
✅ Quantized model serving

❌ Not ideal for frequent model swapping
❌ Overkill for single-user scenarios

## Performance

**70B Q4 AWQ on RTX 4090:**
- Throughput: ~18 tokens/sec
- Latency: 75ms first token
- Memory: ~22GB VRAM

## Quick Start

```bash
docker-compose up -d
curl http://localhost:8080/health
```

## API Example

```bash
curl http://localhost:8080/generate \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": "What is deep learning?",
    "parameters": {"max_new_tokens": 200}
  }'
```

## vs vLLM

| Feature | TGI | vLLM |
|---------|-----|------|
| Speed | ⚡⚡⚡⚡ | ⚡⚡⚡⚡⚡ |
| HF Integration | Excellent | Good |
| Ease of use | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Monitoring | Built-in | Basic |
| Docker focus | Yes | Optional |
