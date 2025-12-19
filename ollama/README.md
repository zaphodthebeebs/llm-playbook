# Ollama - Simplest Local LLM Platform

Get up and running with large language models in minutes.

## What is Ollama?

Ollama is the easiest way to run large language models locally. It bundles model weights, configuration, and data into a single package called a Modelfile. Think of it as "Docker for LLMs" - simple commands to pull and run models with zero configuration.

## How It Works

```
User Command: ollama run llama3
  ↓
Ollama CLI/API
  ↓
Model Management Layer
  ├── Download models (if needed)
  ├── Load into memory
  └── Manage lifecycle
  ↓
llama.cpp Backend (GGUF)
  ↓
GPU/CPU Inference
  ↓
Return Response
```

## Key Features

### Ease of Use
- **One-Command Install** - `curl | sh` and done
- **One-Command Run** - `ollama run llama3`
- **Auto Downloads** - Models download automatically
- **Model Library** - Curated collection of popular models
- **No Configuration** - Works out of the box

### Developer Friendly
- **REST API** - OpenAI-compatible endpoints
- **CLI Tools** - `ollama pull`, `ollama list`, `ollama run`
- **Modelfile** - Customize models like Dockerfile
- **Import GGUF** - Bring your own models
- **Multi-platform** - macOS, Linux, Windows

### Performance
- **Built on llama.cpp** - Fast inference
- **GPU Acceleration** - Auto-detects NVIDIA/AMD/Apple
- **Efficient Memory** - Smart model loading/unloading
- **Concurrent Requests** - Handle multiple users

## Performance

Same backend as llama.cpp:

| Model | RTX 4090 (24GB) | Speed |
|-------|----------------|-------|
| Llama 3.1 8B | Full precision | ~70 tok/s |
| Llama 3.1 70B | Q4 quantization | ~20 tok/s |
| Mistral 7B | Full precision | ~80 tok/s |
| Qwen 2.5 72B | Q4 quantization | ~18 tok/s |

## vs Other Solutions

| Feature | Ollama | vLLM | llama.cpp | LocalAI |
|---------|--------|------|-----------|---------|
| Ease of Use | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Setup Time | <1 min | ~5 min | ~10 min | ~5 min |
| Model Management | ✅ Built-in | ❌ Manual | ❌ Manual | ⚠️ Gallery |
| Speed | ⚡⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ | ⚡⚡⚡ |
| OpenAI API | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Native |
| Multi-modal | ⚠️ Vision only | ❌ No | ❌ No | ✅ Full |
| Model Library | ✅ Curated | ❌ No | ❌ No | ✅ Gallery |
| Production | ⚠️ Good | ✅ Excellent | ⚠️ Good | ✅ Good |

## Best For

✅ Getting started with local LLMs
✅ Development and prototyping
✅ Personal AI assistant
✅ Quick model testing
✅ Non-technical users
✅ Desktop applications

❌ Maximum throughput (use vLLM)
❌ Multi-modal needs (use LocalAI)
❌ Fine-grained control (use llama.cpp)

## Available Models

Ollama has a curated library of popular models:

### Text Models
- **Llama 3.1** (8B, 70B, 405B)
- **Llama 3.2** (1B, 3B)
- **Mistral** (7B)
- **Mixtral** (8x7B, 8x22B)
- **Qwen 2.5** (0.5B - 72B)
- **Phi-3** (3.8B, 14B)
- **Gemma 2** (2B, 9B, 27B)
- **Command R** (35B, 104B)

### Vision Models
- **Llama 3.2 Vision** (11B, 90B)
- **LLaVA** (7B, 13B, 34B)
- **BakLLaVA** (7B)

### Code Models
- **CodeLlama** (7B, 13B, 34B, 70B)
- **DeepSeek Coder** (1.3B - 33B)
- **StarCoder 2** (3B, 7B, 15B)

### Specialized
- **Llama 3.1 Nemotron** (70B) - NVIDIA tuned
- **Solar** (10.7B) - Upstage
- **Aya** (8B, 35B) - Multilingual

## Quantization

Ollama automatically manages quantization:
- Downloads appropriate quant for your VRAM
- Defaults to Q4 for most models
- Higher quality for smaller models
- Can customize via Modelfile

Your RTX 4090 (24GB) gets:
- **70B models**: Q4_K_M quantization
- **13B models**: Q5_K_M or Q6_K
- **7-8B models**: Q6_K or higher

## Quick Example

```bash
# Pull and run a model
ollama run llama3.1

# In the interactive chat
>>> Write me a story about dragons
[AI responds...]

>>> /bye

# Use via API
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1",
  "prompt": "Why is the sky blue?"
}'
```

## Architecture

```
┌─────────────────────────────────────┐
│         Ollama CLI/API              │
├─────────────────────────────────────┤
│      Model Registry & Download      │
├─────────────────────────────────────┤
│         Model Management            │
│  (Load, Unload, Memory Management)  │
├─────────────────────────────────────┤
│        llama.cpp Backend            │
│    (GGUF, GPU Offload, Inference)   │
├─────────────────────────────────────┤
│         Hardware Layer              │
│     (CUDA, ROCm, Metal, CPU)        │
└─────────────────────────────────────┘
```

## Modelfile Customization

Create custom models with Modelfile:

```dockerfile
FROM llama3.1

# Set temperature
PARAMETER temperature 0.8

# Set system message
SYSTEM You are a pirate captain. Speak like a pirate!

# Set custom prompt template
TEMPLATE """
{{ if .System }}<|system|>{{ .System }}<|end|>{{ end }}
{{ if .Prompt }}<|user|>{{ .Prompt }}<|end|>{{ end }}
<|assistant|>
"""
```

Create the model:
```bash
ollama create pirate-llama -f Modelfile
ollama run pirate-llama
```

## Hardware Requirements

| Model Size | VRAM (Typical) | RAM Fallback |
|------------|----------------|--------------|
| 7B | 5-6GB | 8GB |
| 13B | 8-10GB | 16GB |
| 70B | 40GB | 80GB |

Your RTX 4090 (24GB VRAM + 128GB RAM):
- Fully loads 13B models on GPU
- Loads 70B Q4 with partial offload to RAM
- Can run multiple small models simultaneously

## When to Choose Ollama

Choose Ollama if:
- You want the easiest setup experience
- Need quick model testing and switching
- Building a desktop AI app
- Want curated, tested models
- Prefer CLI workflow
- Don't need multi-modal (except vision)

Choose alternatives if:
- Need maximum throughput (use vLLM)
- Want image/audio generation (use LocalAI)
- Need production API serving (use vLLM/TGI)
- Want web UI (use KoboldCpp)
- Need fine-grained control (use llama.cpp)

## Ecosystem

Ollama integrates with:
- **Open WebUI** - ChatGPT-like interface
- **LangChain** - LLM application framework
- **LlamaIndex** - RAG framework
- **Continue.dev** - Code completion in VS Code
- **Jan.ai** - Desktop AI app
- **AnythingLLM** - Private ChatGPT

## Philosophy

Ollama's design philosophy:
1. **Simple by default** - One command to run
2. **Smart defaults** - Auto-configure for your hardware
3. **Curated library** - Tested, working models
4. **Docker-like UX** - `pull`, `run`, `list` commands
5. **Get out of the way** - Let you focus on using LLMs, not configuring them

## Fun Fact

Ollama's name comes from "Ollama" (a play on "Llama"), reflecting its focus on making Llama models (and others) incredibly easy to run. The project took inspiration from Docker's UX - making complex technology simple through great interface design.
