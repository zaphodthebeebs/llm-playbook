# KoboldCpp Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/koboldcpp
docker-compose up -d
```

## Access Web UI

Open browser to:
```
http://localhost:5001
```

## Download Models

KoboldCpp uses GGUF models (same as llama.cpp).

### Method 1: Download via Web UI

1. Open `http://localhost:5001`
2. Click "Load Model"
3. Enter HuggingFace model URL
4. KoboldCpp will download and load it

### Method 2: Manual Download

```bash
# Create models directory
mkdir -p /workspace/llm/models

# Download a model from HuggingFace
# Example: Llama 2 70B Q4_K_M
wget -P /workspace/llm/models \
  https://huggingface.co/TheBloke/Llama-2-70B-Chat-GGUF/resolve/main/llama-2-70b-chat.Q4_K_M.gguf

# Example: Mistral 7B Q5_K_M
wget -P /workspace/llm/models \
  https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF/resolve/main/mistral-7b-instruct-v0.2.Q5_K_M.gguf
```

### Method 3: Using huggingface-cli

```bash
# Install HuggingFace CLI
pip install huggingface-hub

# Download entire model repo
huggingface-cli download TheBloke/Llama-2-70B-Chat-GGUF \
  llama-2-70b-chat.Q4_K_M.gguf \
  --local-dir /workspace/llm/models

# Download with pattern matching
huggingface-cli download TheBloke/Llama-2-13B-chat-GGUF \
  --include "*.Q5_K_M.gguf" \
  --local-dir /workspace/llm/models
```

## Recommended Models for RTX 4090

### 70B Models (Q4 - Fits in VRAM)
```bash
# Llama 2 70B Chat Q4_K_M (~38GB)
https://huggingface.co/TheBloke/Llama-2-70B-Chat-GGUF/blob/main/llama-2-70b-chat.Q4_K_M.gguf

# Llama 3.1 70B Instruct Q4_K_M
https://huggingface.co/bartowski/Meta-Llama-3.1-70B-Instruct-GGUF/blob/main/Meta-Llama-3.1-70B-Instruct-Q4_K_M.gguf
```

### 13B Models (Q5/Q6 - High Quality)
```bash
# Llama 2 13B Q5_K_M (~9GB)
https://huggingface.co/TheBloke/Llama-2-13B-chat-GGUF/blob/main/llama-2-13b-chat.Q5_K_M.gguf

# MythoMax 13B (Creative Writing)
https://huggingface.co/TheBloke/MythoMax-L2-13B-GGUF/blob/main/mythomax-l2-13b.Q5_K_M.gguf
```

### 7-8B Models (Q8 - Best Quality)
```bash
# Llama 3.1 8B Q8_0 (~8.5GB)
https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/blob/main/Meta-Llama-3.1-8B-Instruct-Q8_0.gguf

# Mistral 7B Q6_K (~5.9GB)
https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF/blob/main/mistral-7b-instruct-v0.2.Q6_K.gguf
```

## Load Model in Web UI

1. Open `http://localhost:5001`
2. Click "Load Model" or AI icon
3. Browse to model file OR enter path:
   ```
   /models/llama-2-70b-chat.Q4_K_M.gguf
   ```
4. Set GPU layers:
   - For RTX 4090: Set to **41** (most models)
   - If out of VRAM: Reduce to 35-40
5. Set threads: **8-16** (for i9-14900K)
6. Set context size: **4096** or **8192**
7. Click "Load"

## Configuration

### GPU Layers
How many layers to offload to GPU:
- **41**: Most 70B Q4 models fit
- **35**: If you get VRAM errors
- **0**: CPU only (slow)

Check VRAM usage:
```bash
nvidia-smi
```

### Threads
CPU threads to use (when not on GPU):
- **8**: Good default for i9-14900K
- **16**: Maximum for your CPU
- Lower if system becomes sluggish

### Context Size
Maximum conversation length:
- **2048**: Short chats
- **4096**: Standard (recommended)
- **8192**: Long conversations
- **16384**: Very long (uses more VRAM)

## Docker Compose Options

Edit `docker-compose.yml`:

```yaml
environment:
  # Default model to load on startup
  - MODEL_PATH=/models/llama-2-70b-chat.Q4_K_M.gguf
  # GPU layers (0-41)
  - GPU_LAYERS=41
  # Context size
  - CONTEXT_SIZE=4096
  # CPU threads
  - THREADS=8
```

Restart:
```bash
docker-compose restart
```

## Verify Installation

```bash
# Check container is running
docker ps | grep koboldcpp

# Check logs
docker logs -f koboldcpp

# Check web UI
curl http://localhost:5001
```

## Using OpenAI-Compatible API

KoboldCpp also provides an API endpoint (optional):

```bash
curl http://localhost:5001/api/v1/generate \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Once upon a time",
    "max_length": 100,
    "temperature": 0.7
  }'
```

Or OpenAI-compatible:
```bash
curl http://localhost:5001/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "koboldcpp",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Troubleshooting

### Out of VRAM
```
Error: failed to allocate memory
```

**Fix:**
- Reduce GPU layers (try 35 instead of 41)
- Use smaller quantization (Q4 instead of Q5)
- Reduce context size (4096 instead of 8192)

### Slow Generation
```
Generating at <10 tokens/sec
```

**Fix:**
- Increase GPU layers
- Check GPU is being used: `nvidia-smi`
- Ensure NVIDIA runtime is enabled in docker-compose

### Can't Load Model
```
Error: failed to load model
```

**Fix:**
- Check model file exists in `/workspace/llm/models`
- Verify file isn't corrupted (re-download)
- Check file permissions

### Web UI Not Accessible
```
Can't connect to localhost:5001
```

**Fix:**
```bash
# Check container is running
docker ps | grep koboldcpp

# Check logs for errors
docker logs koboldcpp

# Restart
docker-compose restart
```

## Update

```bash
docker-compose pull
docker-compose up -d
```

## Uninstall

```bash
docker-compose down

# Remove models (optional)
rm -rf /workspace/llm/models
```

## Tips

- **Start with smaller models** (7B/13B) to test setup
- **Use Q4_K_M for 70B** - best balance of quality/VRAM
- **Use Q8_0 for 8B** - maximum quality, fits easily
- **Save your stories** - Use the save button in UI
- **Experiment with samplers** - Each model responds differently
- **Create character cards** - Reuse favorite personalities

## Resources

- [KoboldCpp GitHub](https://github.com/LostRuins/koboldcpp)
- [GGUF Models (TheBloke)](https://huggingface.co/TheBloke)
- [Model Search (HuggingFace)](https://huggingface.co/models?library=gguf)
