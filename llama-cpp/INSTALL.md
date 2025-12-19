# llama.cpp Installation

## Prerequisites

Download GGUF models first:

```bash
# Create model directory
mkdir -p /workspace/llm/models/gguf

# Download using HuggingFace CLI
pip install huggingface-hub
huggingface-cli download TheBloke/Llama-2-70B-Chat-GGUF \
  llama-2-70b-chat.Q4_K_M.gguf \
  --local-dir /workspace/llm/models/gguf
```

Or download manually from https://huggingface.co/TheBloke

## Docker Install

```bash
cd /workspace/llm/llama-cpp

# Update MODEL path in docker-compose.yml
# Then start:
docker-compose up -d
```

## Verify

```bash
curl http://localhost:8081/v1/models
```

## Configuration

Edit `docker-compose.yml`:

```yaml
environment:
  - MODEL=/models/your-model.Q4_K_M.gguf
  - N_GPU_LAYERS=41  # Adjust based on VRAM
  - CTX_SIZE=8192    # Context window
```

## GPU Layer Offloading

Calculate layers for your VRAM:

**RTX 4090 (24GB):**
- 70B Q4: 41 layers
- 70B Q5: 35 layers
- 34B Q4: Full model (all layers)
- 13B Q4: Full model

**Less VRAM:**
- 12GB: ~20 layers for 70B
- 8GB: ~12 layers for 70B or full 13B

## Finding GGUF Models

Search HuggingFace:
```
https://huggingface.co/models?search=GGUF
```

Best sources:
- `TheBloke` - Most comprehensive
- `bartowski` - Latest models
- `mradermacher` - High quality

## Troubleshooting

**Model not found:** Check volume mount path
**Out of memory:** Reduce N_GPU_LAYERS
**Slow:** Increase N_GPU_LAYERS for more GPU usage
