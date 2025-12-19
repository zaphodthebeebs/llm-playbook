# KoboldCpp - Interactive LLM with Web UI

llama.cpp with a built-in web interface for interactive storytelling and role-playing.

## What is KoboldCpp?

KoboldCpp is a user-friendly wrapper around llama.cpp that adds a polished web UI designed for creative writing, interactive fiction, and role-playing. It's the easiest way to run local LLMs with a full-featured interface - no coding required.

## How It Works

```
User → Web Browser (localhost:5001)
  ↓
KoboldCpp Web UI
  ├── Chat Interface
  ├── Story Mode
  ├── Adventure Mode
  └── Settings Panel
  ↓
llama.cpp Backend (GGUF)
  ↓
GPU/CPU Inference
  ↓
Return Generated Text
```

## Key Features

### Interactive Modes
- **Chat Mode** - Standard chatbot interface
- **Story Mode** - Collaborative story writing
- **Adventure Mode** - Text-based RPG gameplay
- **Instruct Mode** - Instruction following

### Web UI Features
- **No Coding Required** - Everything in the browser
- **Memory/Lore Books** - Track characters, world details
- **Author's Note** - Guide story direction
- **Token Probability Viewer** - See generation choices
- **Multiple Samplers** - Fine-tune generation
- **Save/Load Stories** - Resume sessions
- **Character Cards** - Pre-defined personalities
- **World Info** - Context injection

### Technical Features
- **GGUF Support** - All llama.cpp quantizations
- **GPU Acceleration** - CUDA/ROCm/Vulkan/Metal
- **Multi-threading** - CPU parallelization
- **Smart Context** - Automatic context management
- **Streaming** - Real-time generation
- **OpenAI API** - Optional API endpoint

## Performance

Same as llama.cpp (uses it as backend):

| Model | RTX 4090 | Quantization | Speed |
|-------|----------|--------------|-------|
| Llama 3.1 8B | 24GB VRAM | Q8_0 | ~80 tok/s |
| Llama 2 13B | 24GB VRAM | Q5_K_M | ~50 tok/s |
| Llama 2 70B | 24GB VRAM | Q4_K_M | ~20 tok/s |

## vs Other Solutions

| Feature | KoboldCpp | Ollama | llama.cpp |
|---------|-----------|--------|-----------|
| Web UI | ✅ Full-featured | ❌ No | ❌ No |
| Story Writing | ✅ Optimized | ⚠️ Basic | ❌ No |
| Role-Playing | ✅ Excellent | ⚠️ Possible | ❌ No |
| Memory/Lore | ✅ Built-in | ❌ No | ❌ No |
| Easy Setup | ✅ Very Easy | ✅ Very Easy | ⚠️ Technical |
| API Only | ⚠️ Optional | ✅ Yes | ✅ Yes |
| Speed | ✅ Fast | ✅ Fast | ✅ Fastest |

## Best For

✅ Interactive storytelling
✅ Role-playing games (RPG)
✅ Creative writing
✅ Character chat
✅ Non-technical users
✅ Desktop use (local web UI)

❌ Production API serving (use vLLM)
❌ Headless servers (use llama.cpp)
❌ Maximum performance (use llama.cpp)
❌ Multi-user deployments

## Supported Models

Any GGUF model from llama.cpp:
- Llama 3.1/3/2
- Mistral/Mixtral
- Qwen
- Yi
- DeepSeek
- Phi-3
- Community fine-tunes (MythoMax, etc)

## Quantization Guide

| Quantization | Size (70B) | Quality | Speed | Recommended |
|--------------|------------|---------|-------|-------------|
| Q2_K | ~26GB | Poor | Fastest | ❌ |
| Q4_K_M | ~38GB | Good | Fast | ✅ Best balance |
| Q5_K_M | ~48GB | Very Good | Medium | ✅ Quality focus |
| Q6_K | ~56GB | Excellent | Slower | ⚠️ If fits VRAM |
| Q8_0 | ~70GB | Near-perfect | Slowest | ❌ Too large |

For your RTX 4090 (24GB):
- **8B models**: Q8_0 (best quality)
- **13B models**: Q5_K_M or Q6_K
- **70B models**: Q4_K_M

## Quick Example

1. Start KoboldCpp
2. Open browser to `http://localhost:5001`
3. Select mode (Chat/Story/Adventure)
4. Start typing or click "Generate"
5. Use sliders to adjust temperature, top_p, etc.
6. Save your session when done

## UI Features Explained

### Memory
Permanently remembered context (character info, world details)

### Author's Note
Injected before generation to guide the AI's output

### World Info
Conditionally activated lore (triggers on keywords)

### Token Probability
See what the AI considered generating (educational)

### Samplers
Control randomness, repetition, and generation quality

## Creative Writing Presets

### Balanced
- Temperature: 0.7
- Top P: 0.9
- Repetition Penalty: 1.1

### Creative
- Temperature: 0.9
- Top P: 0.95
- Repetition Penalty: 1.15
- Typical P: 0.9

### Coherent
- Temperature: 0.5
- Top P: 0.85
- Repetition Penalty: 1.2
- Min P: 0.1

## Hardware Requirements

| Model Size | VRAM (Q4) | VRAM (Q8) | RAM Fallback |
|------------|-----------|-----------|--------------|
| 7B | 4GB | 7GB | 8GB |
| 13B | 7GB | 13GB | 16GB |
| 70B | 35GB | 70GB | 80GB |

Your RTX 4090 (24GB VRAM + 128GB RAM):
- Can fully load 13B Q8 on GPU
- Can load 70B Q4 on GPU
- Can offload overflow to RAM (slower but works)

## When to Choose KoboldCpp

Choose KoboldCpp if:
- You want a web UI for story writing
- Doing creative writing or role-playing
- Need memory/lore book features
- Prefer GUI over API
- Want easy local setup
- Desktop/personal use

Choose alternatives if:
- Need production API (use vLLM)
- Want maximum speed (use llama.cpp directly)
- Building applications (use Ollama)
- Multi-user deployment (use LocalAI)

## Fun Fact

KoboldCpp is named after the Kobold from D&D - a small, mischievous creature that loves stories and treasure. The community uses it heavily for AI Dungeon-style games and collaborative storytelling!
