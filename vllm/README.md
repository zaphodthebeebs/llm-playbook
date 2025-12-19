# vLLM - Fast LLM Inference Engine

vLLM is a high-throughput and memory-efficient inference engine for Large Language Models.

## What is vLLM?

vLLM is developed by researchers at UC Berkeley and uses **PagedAttention** - a novel attention algorithm that efficiently manages attention keys and values in GPU memory. This allows vLLM to achieve:

- **24x higher throughput** than HuggingFace Transformers
- **2x faster** than FasterTransformer
- Supports **continuous batching** for better GPU utilization
- **OpenAI-compatible API** for easy integration

## How It Works

### PagedAttention Algorithm

Traditional LLM serving stores attention KV cache as continuous memory blocks, leading to:
- Memory fragmentation
- Wasted GPU memory
- Poor batching efficiency

vLLM's PagedAttention:
1. Divides KV cache into fixed-size blocks
2. Stores blocks in non-contiguous memory (like OS paging)
3. Allows efficient memory sharing across requests
4. Enables dynamic batching

```
Traditional: [████████▓▓▓▓▓▓▓▓░░░░░░░░] ← Wasted 40%
vLLM:        [████████][████████][████] ← Efficient paging
```

### Continuous Batching

- Requests arrive asynchronously
- vLLM batches them dynamically
- New requests join ongoing batches
- Better GPU utilization vs static batching

## Key Features

### ✅ Supported Models

- **LLaMA** family (1, 2, 3, 3.1)
- **Mistral** and **Mixtral**
- **Qwen** (1.5, 2, 2.5)
- **Phi-3**
- **Gemma**
- **Yi**
- **DeepSeek**
- And 50+ more on HuggingFace

### ✅ Quantization Support

- **AWQ** (4-bit, fastest)
- **GPTQ** (4-bit, good quality)
- **SqueezeLLM** (research)
- **FP8** (NVIDIA H100)

### ✅ Features

- OpenAI-compatible `/v1/completions` and `/v1/chat/completions` endpoints
- Streaming responses
- Multi-GPU tensor parallelism
- Pipeline parallelism for huge models
- Prefix caching (shared prompts)
- Speculative decoding

## Architecture

```
┌─────────────────────────────────────────┐
│          Client Request                 │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│     OpenAI API Server (FastAPI)         │
│  /v1/completions  /v1/chat/completions  │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│       Request Scheduler                 │
│   (Continuous Batching + Priority)      │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│      PagedAttention Engine              │
│  ┌────────┐  ┌────────┐  ┌────────┐    │
│  │ Block  │  │ Block  │  │ Block  │    │
│  │  KV    │  │  KV    │  │  KV    │    │
│  └────────┘  └────────┘  └────────┘    │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         GPU (CUDA Kernels)              │
│           RTX 4090 24GB                 │
└─────────────────────────────────────────┘
```

## Performance Characteristics

### Throughput (70B Q4 model on RTX 4090)

| Batch Size | Tokens/sec | Latency |
|------------|-----------|---------|
| 1 | 15 | 67ms |
| 4 | 45 | 89ms |
| 8 | 80 | 100ms |
| 16 | 140 | 114ms |

### Memory Usage

- **Model weights**: ~40GB (70B Q4 AWQ)
- **KV cache**: ~8GB (with PagedAttention efficiency)
- **System overhead**: ~2GB
- **Total**: ~50GB (can run on 24GB VRAM with CPU offload)

## Use Cases

### ✅ Best For:
- Production API serving
- High concurrent users
- Batch processing
- Low latency requirements
- OpenAI API compatibility needed

### ❌ Not Ideal For:
- Interactive chat UI (use Ollama or KoboldCpp)
- Frequent model swapping
- Non-HuggingFace models
- CPU-only inference

## Comparison to Ollama

| Feature | vLLM | Ollama |
|---------|------|--------|
| **Speed** | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ |
| **Throughput** | High (batching) | Medium |
| **API** | OpenAI compatible | Custom + OpenAI |
| **Model management** | Manual | Automatic |
| **Ease of use** | Medium | High |
| **Quantization** | AWQ, GPTQ | GGUF |
| **Multi-GPU** | Excellent | Limited |

## When to Use vLLM

Choose vLLM when you need:
1. **Maximum throughput** - Serving many users
2. **Production stability** - Battle-tested at scale
3. **OpenAI compatibility** - Drop-in replacement
4. **Low latency** - Fast first token time
5. **Efficient GPU usage** - PagedAttention magic

## Example API Usage

### Completions
```bash
curl http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "prompt": "Explain quantum computing in simple terms:",
    "max_tokens": 100,
    "temperature": 0.7
  }'
```

### Chat
```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [
      {"role": "user", "content": "What is the meaning of life?"}
    ],
    "temperature": 0.8,
    "max_tokens": 150
  }'
```

### Streaming
```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-70B-Instruct",
    "messages": [{"role": "user", "content": "Write a poem"}],
    "stream": true
  }'
```

## Integration Examples

### Python (OpenAI SDK)
```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="dummy"  # vLLM doesn't require auth
)

response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[
        {"role": "user", "content": "Hello!"}
    ]
)
print(response.choices[0].message.content)
```

### JavaScript
```javascript
const response = await fetch('http://localhost:8000/v1/chat/completions', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    model: 'meta-llama/Llama-3.1-70B-Instruct',
    messages: [{ role: 'user', content: 'Hello!' }]
  })
});

const data = await response.json();
console.log(data.choices[0].message.content);
```

## Advanced Features

### Tensor Parallelism (Multi-GPU)
```bash
# Split across 2 GPUs
vllm serve model --tensor-parallel-size 2
```

### Prefix Caching
```bash
# Automatically caches common prefixes
# Great for chatbots with system prompts
vllm serve model --enable-prefix-caching
```

### Speculative Decoding
```bash
# Use small model to predict, large model to verify
vllm serve large-model \
  --speculative-model small-model \
  --num-speculative-tokens 5
```

## Monitoring

vLLM exposes metrics on `/metrics` endpoint (Prometheus format):

- `vllm:num_requests_running`
- `vllm:num_requests_waiting`
- `vllm:gpu_cache_usage_perc`
- `vllm:time_to_first_token_seconds`
- `vllm:time_per_output_token_seconds`

## Troubleshooting

### Out of Memory
- Reduce `--max-model-len`
- Enable `--gpu-memory-utilization 0.8` (default 0.9)
- Use smaller quantization (AWQ 4-bit)

### Slow Performance
- Check `nvidia-smi` - is GPU at 100%?
- Increase `--max-num-seqs` for more batching
- Use `--enforce-eager` if compilation issues

### Model Not Loading
- Check model path/HuggingFace model ID
- Verify CUDA drivers: `nvidia-smi`
- Check disk space: `df -h`

## Resources

- [Official Documentation](https://docs.vllm.ai/)
- [GitHub Repository](https://github.com/vllm-project/vllm)
- [Research Paper](https://arxiv.org/abs/2309.06180)
- [Model Compatibility](https://docs.vllm.ai/en/latest/models/supported_models.html)
