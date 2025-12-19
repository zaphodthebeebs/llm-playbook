# LLM Inference Engines Playbook

A comprehensive guide and setup repository for running large language models locally. Compare and deploy multiple LLM inference engines optimized for **i9-14900K, 128GB RAM, RTX 4090 (24GB VRAM)**.

## 🚀 Available Services

### LLM Inference Engines

| Engine | Port | Best For | Speed | Ease of Use | Documentation |
|--------|------|----------|-------|-------------|---------------|
| [**Ollama**](./ollama/) | 11434 | Getting Started, Easy Setup | ⚡⚡⚡ | ⭐⭐⭐⭐⭐ | [📖 Read More](./ollama/README.md) |
| [**vLLM**](./vllm/) | 8000 | Production, High Throughput | ⚡⚡⚡⚡⚡ | ⭐⭐⭐ | [📖 Read More](./vllm/README.md) |
| [**Text Generation Inference**](./text-generation-inference/) | 8080 | HuggingFace Models | ⚡⚡⚡⚡ | ⭐⭐⭐⭐ | [📖 Read More](./text-generation-inference/README.md) |
| [**llama.cpp**](./llama-cpp/) | 8081 | GGUF Models, Flexibility | ⚡⚡⚡ | ⭐⭐⭐⭐ | [📖 Read More](./llama-cpp/README.md) |
| [**LocalAI**](./localai/) | 8082 | OpenAI API Drop-in, Multi-modal | ⚡⚡⚡ | ⭐⭐⭐⭐⭐ | [📖 Read More](./localai/README.md) |
| [**Aphrodite Engine**](./aphrodite-engine/) | 8083 | Chat, Creative Writing | ⚡⚡⚡⚡⚡ | ⭐⭐⭐ | [📖 Read More](./aphrodite-engine/README.md) |
| [**KoboldCpp**](./koboldcpp/) | 5001 | RPG, Story Writing, Web UI | ⚡⚡⚡ | ⭐⭐⭐⭐⭐ | [📖 Read More](./koboldcpp/README.md) |

### Supporting Services

| Service | Port | Best For | Documentation |
|---------|------|----------|---------------|
| [**Stable Diffusion**](./stable-diffusion/) | 7860 | AI Image Generation | [📖 Read More](./stable-diffusion/README.md) |
| [**n8n**](./n8n/) | 5678 | Workflow Automation, LLM Orchestration | [📖 Read More](./n8n/README.md) |

## 📚 Quick Navigation

### By Use Case

**🎯 Just Getting Started?**
- Start with [**Ollama**](./ollama/) - Easiest setup, one command to run
- Or try [**LocalAI**](./localai/) - OpenAI-compatible, multi-modal support

**🚄 Need Maximum Speed?**
- Use [**vLLM**](./vllm/) - Fastest throughput with PagedAttention
- Or [**Aphrodite Engine**](./aphrodite-engine/) - vLLM fork with enhanced sampling

**💻 Writing Code?**
- [**Ollama**](./ollama/) with CodeLlama model
- [**vLLM**](./vllm/) for production code generation API

**📖 Creative Writing / Storytelling?**
- [**KoboldCpp**](./koboldcpp/) - Interactive web UI, memory/lore books
- [**Aphrodite Engine**](./aphrodite-engine/) - Advanced sampling for creativity

**🎨 Multi-Modal (Text + Images + Audio)?**
- [**LocalAI**](./localai/) - Text, image, audio generation in one API
- [**Stable Diffusion**](./stable-diffusion/) - Dedicated AI image generation

**🔄 Workflow Automation?**
- [**n8n**](./n8n/) - Build LLM workflows, connect services, automate tasks

**⚙️ Maximum Control & Flexibility?**
- [**llama.cpp**](./llama-cpp/) - Fine-grained control over everything
- [**Text Generation Inference**](./text-generation-inference/) - HuggingFace ecosystem

## 🏗️ Repository Structure

```
llm/
├── README.md                          # This file - main overview
├── models/                            # Shared models directory
│   ├── huggingface/                   # HuggingFace format models
│   └── gguf/                          # GGUF quantized models
│
├── ollama/                            # Easiest local LLM platform
│   ├── README.md                      # What is Ollama?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── vllm/                              # High-performance inference
│   ├── README.md                      # What is vLLM?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── text-generation-inference/         # HuggingFace TGI
│   ├── README.md                      # What is TGI?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── llama-cpp/                         # GGUF model inference
│   ├── README.md                      # What is llama.cpp?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── localai/                           # OpenAI-compatible multi-modal
│   ├── README.md                      # What is LocalAI?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── aphrodite-engine/                  # Creative writing optimized
│   ├── README.md                      # What is Aphrodite?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── koboldcpp/                         # Interactive story writing
│   ├── README.md                      # What is KoboldCpp?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
├── stable-diffusion/                  # AI image generation
│   ├── README.md                      # What is Stable Diffusion?
│   ├── INSTALL.md                     # Installation guide
│   ├── USAGE.md                       # Usage examples & API
│   └── docker-compose.yml             # Docker setup
│
└── n8n/                               # Workflow automation
    ├── README.md                      # What is n8n?
    ├── INSTALL.md                     # Installation guide
    ├── USAGE.md                       # Usage examples & API
    └── docker-compose.yml             # Docker setup
```

## 🎯 Quick Start

### 1. Choose Your Engine

Read the comparison table above and pick an engine based on your use case.

### 2. Install & Run

Each engine has detailed documentation:

```bash
# Example: Start Ollama
cd /workspace/llm/ollama
docker-compose up -d

# Pull a model
docker exec -it ollama ollama pull llama3.1

# Test it
docker exec -it ollama ollama run llama3.1
```

### 3. Access the API

All engines provide OpenAI-compatible APIs:

```python
from openai import OpenAI

# Works with any engine!
client = OpenAI(
    base_url="http://localhost:<PORT>/v1",
    api_key="dummy"
)

response = client.chat.completions.create(
    model="model-name",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## ⚙️ Hardware Optimization

Your system: **i9-14900K, 128GB RAM, RTX 4090 (24GB VRAM)**

### What You Can Run

| Model Size | Quantization | Where | Performance |
|------------|--------------|-------|-------------|
| 7-8B | FP16 | GPU (7GB) | ~70-80 tok/s |
| 13B | FP16 | GPU (13GB) | ~50-60 tok/s |
| 70B | Q4 | GPU (20GB) | ~18-22 tok/s |
| 70B | Q8 | GPU+RAM | ~10-15 tok/s |
| 405B | Q4 | RAM+GPU | ~3-5 tok/s |

### Recommended Models for RTX 4090

**Best Quality/Speed Balance:**
- Llama 3.1 70B Q4 (~20 tok/s)
- Qwen 2.5 72B Q4 (~18 tok/s)

**Maximum Speed:**
- Llama 3.1 8B FP16 (~75 tok/s)
- Mistral 7B FP16 (~80 tok/s)

**Best Quality (fits in VRAM):**
- Llama 3.1 13B FP16 (~55 tok/s)

## 🔧 Running Multiple Engines

All engines use unique ports - run them simultaneously:

```bash
# Start multiple engines
cd /workspace/llm
docker-compose -f ollama/docker-compose.yml up -d
docker-compose -f vllm/docker-compose.yml up -d
docker-compose -f koboldcpp/docker-compose.yml up -d

# Check all are running
docker ps
```

### Port Reference

| Port | Service | Type |
|------|---------|------|
| 11434 | Ollama | OpenAI + Native API |
| 8000 | vLLM | OpenAI Compatible API |
| 8080 | Text Generation Inference | Custom + OpenAI API |
| 8081 | llama.cpp | OpenAI Compatible API |
| 8082 | LocalAI | OpenAI Compatible API |
| 8083 | Aphrodite Engine | OpenAI Compatible API |
| 5001 | KoboldCpp | Web UI + OpenAI API |
| 7860 | Stable Diffusion | Web UI + REST API |
| 5678 | n8n | Web UI + REST API |
| **3000** | **Open WebUI (Ollama)** | **ChatGPT-like Interface** |
| **3001** | **Open WebUI (vLLM)** | **ChatGPT-like Interface** |
| **3002** | **Open WebUI (LocalAI)** | **ChatGPT-like Interface** |

## 📊 Performance Comparison

### Throughput (70B Q4 Model)

| Engine | Tokens/sec | Concurrent Requests | Memory |
|--------|-----------|---------------------|--------|
| vLLM | ~22 | 32+ | 20GB VRAM |
| Aphrodite | ~20 | 32+ | 20GB VRAM |
| TGI | ~18 | 16+ | 20GB VRAM |
| llama.cpp | ~17 | 8 | 18GB VRAM |
| Ollama | ~16 | 4 | 18GB VRAM |
| LocalAI | ~15 | 8 | 18GB VRAM |
| KoboldCpp | ~15 | 1 | 18GB VRAM |

### Feature Comparison

| Feature | Ollama | vLLM | TGI | llama.cpp | LocalAI | Aphrodite | KoboldCpp |
|---------|--------|------|-----|-----------|---------|-----------|-----------|
| **OpenAI API** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Easy Setup** | ✅✅✅ | ⚠️ | ⚠️ | ⚠️ | ✅✅ | ⚠️ | ✅✅ |
| **Model Library** | ✅ | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| **Max Speed** | ⚠️ | ✅✅✅ | ✅✅ | ✅ | ⚠️ | ✅✅✅ | ⚠️ |
| **Web UI** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅✅✅ |
| **Images** | ⚠️ Vision | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| **Audio** | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| **Embeddings** | ✅ | ❌ | ✅ | ❌ | ✅ | ❌ | ❌ |
| **Streaming** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **GPU Offload** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

## 🛠️ Common Operations

### Check GPU Usage

```bash
# Real-time GPU monitoring
nvidia-smi -l 1

# Check VRAM usage
watch -n 1 nvidia-smi
```

### View Logs

```bash
# View logs for specific engine
docker logs -f ollama
docker logs -f vllm
docker logs -f koboldcpp
```

### Stop All Engines

```bash
# Stop all running containers
docker stop $(docker ps -q)

# Or stop specific engine
docker-compose -f ollama/docker-compose.yml down
```

### Health Checks

```bash
# Ollama
curl http://localhost:11434

# vLLM
curl http://localhost:8000/v1/models

# Text Generation Inference
curl http://localhost:8080/health

# llama.cpp
curl http://localhost:8081/health

# LocalAI
curl http://localhost:8082/readyz

# Aphrodite
curl http://localhost:8083/v1/models

# KoboldCpp
curl http://localhost:5001/api/v1/model

# Stable Diffusion
curl http://localhost:7860/sdapi/v1/sd-models

# n8n
curl http://localhost:5678
```

## 📖 Documentation Links

### Installation Guides

**LLM Engines:**
- [Ollama Installation](./ollama/INSTALL.md)
- [vLLM Installation](./vllm/INSTALL.md)
- [Text Generation Inference Installation](./text-generation-inference/INSTALL.md)
- [llama.cpp Installation](./llama-cpp/INSTALL.md)
- [LocalAI Installation](./localai/INSTALL.md)
- [Aphrodite Engine Installation](./aphrodite-engine/INSTALL.md)
- [KoboldCpp Installation](./koboldcpp/INSTALL.md)

**Supporting Services:**
- [Stable Diffusion Installation](./stable-diffusion/INSTALL.md)
- [n8n Installation](./n8n/INSTALL.md)

### Usage Guides

**LLM Engines:**
- [Ollama Usage & API](./ollama/USAGE.md)
- [vLLM Usage & API](./vllm/USAGE.md)
- [Text Generation Inference Usage & API](./text-generation-inference/USAGE.md)
- [llama.cpp Usage & API](./llama-cpp/USAGE.md)
- [LocalAI Usage & API](./localai/USAGE.md)
- [Aphrodite Engine Usage & API](./aphrodite-engine/USAGE.md)
- [KoboldCpp Usage & API](./koboldcpp/USAGE.md)

**Supporting Services:**
- [Stable Diffusion Usage & API](./stable-diffusion/USAGE.md)
- [n8n Usage & Workflows](./n8n/USAGE.md)

## 🎓 Learning Path

### Beginner
1. Start with **Ollama** - Learn the basics
2. Try **KoboldCpp** - Interactive web UI
3. Experiment with different models

### Intermediate
4. Set up **llama.cpp** - Understand GGUF quantization
5. Try **LocalAI** - Explore multi-modal capabilities
6. Compare performance across engines

### Advanced
7. Deploy **vLLM** - Production-grade serving
8. Use **Aphrodite** - Advanced sampling techniques
9. Benchmark and optimize for your use case

## 🔗 External Resources

### Official Documentation
- [Ollama](https://ollama.com/)
- [vLLM Docs](https://docs.vllm.ai/)
- [Text Generation Inference](https://huggingface.co/docs/text-generation-inference/)
- [llama.cpp GitHub](https://github.com/ggerganov/llama.cpp)
- [LocalAI Docs](https://localai.io/)
- [Aphrodite Engine GitHub](https://github.com/PygmalionAI/aphrodite-engine)
- [KoboldCpp GitHub](https://github.com/LostRuins/koboldcpp)

### Model Sources
- [Ollama Model Library](https://ollama.com/library)
- [HuggingFace Models](https://huggingface.co/models)
- [GGUF Models (TheBloke)](https://huggingface.co/TheBloke)
- [LocalAI Model Gallery](https://models.localai.io/)

### Community
- [r/LocalLLaMA](https://reddit.com/r/LocalLLaMA)
- [HuggingFace Discord](https://discord.gg/hugging-face)
- [Ollama Discord](https://discord.gg/ollama)

## 🤝 Contributing

This is a personal playbook, but feel free to adapt it for your own use!

## 📝 License

Documentation and configurations are provided as-is for educational purposes. Each LLM engine has its own license - please refer to their respective repositories.

---

**System Specs:** i9-14900K | 128GB RAM | RTX 4090 24GB VRAM

Last Updated: 2025-12-19
