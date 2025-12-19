# Aphrodite Engine - vLLM Fork for Creative Writing

A fork of vLLM optimized for chat applications and creative writing.

## What is Aphrodite Engine?

Aphrodite Engine is a community-maintained fork of vLLM that adds additional features specifically for interactive chat, role-playing, and creative writing use cases. It maintains vLLM's high-performance PagedAttention while adding extra sampling parameters and quality-of-life improvements.

## How It Works

```
Client Request
  ↓
Aphrodite API
  ↓
vLLM Core + Enhanced Sampling
  ↓
PagedAttention Memory Management
  ↓
GPU Inference (CUDA/ROCm)
  ↓
Return Response
```

## Key Features

### From vLLM Base
- **PagedAttention** - Efficient memory management
- **Continuous Batching** - High throughput
- **OpenAI Compatible API** - Drop-in replacement
- **Fast Inference** - Same speed as vLLM

### Aphrodite Additions
- **Enhanced Sampling** - More creative control parameters
  - `repetition_penalty`
  - `frequency_penalty`
  - `presence_penalty`
  - `min_p` sampling
  - `typical_p` sampling
  - `tfs` (tail-free sampling)
  - `top_a` sampling
- **Better Chat Templates** - More model template support
- **Logit Bias** - Fine-grained output control
- **Extended Stop Sequences** - More flexible stopping
- **Community Models** - Easier support for community fine-tunes

## Performance

Same as vLLM (inherits core engine):

| Model | RTX 4090 (24GB) | Speed |
|-------|----------------|-------|
| Llama 3.1 8B | Q4/Q8 | ~60 tok/s |
| Llama 3.1 70B | Q4 | ~20 tok/s |
| Mistral 7B | FP16 | ~80 tok/s |

## vs vLLM

| Feature | Aphrodite | vLLM |
|---------|-----------|------|
| Core Speed | ✅ Same | ✅ Same |
| Memory Efficiency | ✅ Same | ✅ Same |
| Sampling Options | ✅ Extended | ⚠️ Standard |
| Chat Templates | ✅ More Models | ⚠️ Common Models |
| Community Support | ✅ Yes | ⚠️ Enterprise Focus |
| Updates | ⚠️ Slower | ✅ Frequent |

## Best For

✅ Creative writing and role-play
✅ Chat applications
✅ Fine-tuned community models
✅ Need advanced sampling parameters
✅ Interactive storytelling

❌ Maximum cutting-edge performance (use vLLM)
❌ Production enterprise use (use vLLM)
❌ Latest model support (use vLLM)

## Supported Models

- Llama 3.1/3/2
- Mistral/Mixtral
- Yi
- Qwen
- DeepSeek
- Many community fine-tunes

## Quantization Support

- AWQ (4-bit, fastest)
- GPTQ (4-bit, good quality)
- bitsandbytes (4/8-bit)
- FP16/BF16 (full precision)

## Quick Example

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8083/v1",
    api_key="dummy"
)

response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Tell me a creative story"}],
    temperature=0.9,
    top_p=0.95,
    repetition_penalty=1.15,  # Aphrodite-specific
    min_p=0.05  # Aphrodite-specific
)
print(response.choices[0].message.content)
```

## Hardware Requirements

Same as vLLM:

| Model Size | VRAM (Quantized) | VRAM (FP16) |
|------------|------------------|-------------|
| 7B | 4GB (Q4) | 14GB |
| 13B | 7GB (Q4) | 26GB |
| 70B | 35GB (Q4) | 140GB+ |

Your RTX 4090 (24GB) can run:
- 7B/13B models at FP16
- 70B models at Q4
- 8B models at Q8 (high quality)

## When to Choose Aphrodite

Choose Aphrodite if:
- You need advanced sampling for creative writing
- Working with community fine-tuned models
- Want more control over output generation
- Doing interactive chat/RP applications

Choose vLLM if:
- Need latest features and model support
- Production deployment
- Maximum performance is critical
- Enterprise support needed
