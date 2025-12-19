# Ollama Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/ollama
docker-compose up -d
```

## Verify

```bash
# Check service is running
curl http://localhost:11434

# Should return: "Ollama is running"
```

## Install Models

Ollama makes model installation incredibly simple.

### Pull a Model

```bash
# Enter the container
docker exec -it ollama bash

# Pull a model (downloads automatically)
ollama pull llama3.1

# Pull specific size
ollama pull llama3.1:70b

# Pull a different model
ollama pull mistral
ollama pull qwen2.5:72b
ollama pull codellama
```

### List Available Models Online

Visit: https://ollama.com/library

Or check popular models:
```bash
# Popular models
ollama pull llama3.1        # Meta's Llama 3.1 8B
ollama pull llama3.1:70b    # Llama 3.1 70B
ollama pull mistral         # Mistral 7B
ollama pull mixtral:8x7b    # Mixtral 8x7B
ollama pull qwen2.5:72b     # Qwen 2.5 72B
ollama pull codellama       # Code Llama
ollama pull phi3            # Microsoft Phi-3
ollama pull gemma2          # Google Gemma 2
```

### List Installed Models

```bash
docker exec -it ollama ollama list
```

## Recommended Models for RTX 4090

### 70B Models (Best for Your GPU)
```bash
# Llama 3.1 70B (Q4, ~40GB)
ollama pull llama3.1:70b

# Qwen 2.5 72B (Q4, ~42GB)
ollama pull qwen2.5:72b

# Llama 3.1 Nemotron 70B (NVIDIA tuned)
ollama pull nemotron
```

### 8B Models (Fast, High Quality)
```bash
# Llama 3.1 8B (default)
ollama pull llama3.1

# Qwen 2.5 7B
ollama pull qwen2.5:7b

# Mistral 7B
ollama pull mistral
```

### Vision Models
```bash
# Llama 3.2 Vision 11B
ollama pull llama3.2-vision

# LLaVA (image understanding)
ollama pull llava
```

### Code Models
```bash
# Code Llama 70B
ollama pull codellama:70b

# DeepSeek Coder
ollama pull deepseek-coder:33b

# StarCoder 2
ollama pull starcoder2
```

## Quick Start

```bash
# Run interactive chat
docker exec -it ollama ollama run llama3.1

# Chat with the model
>>> Hello! Tell me a joke.
[AI responds...]

>>> /bye
```

## Configuration

### Default Settings

Ollama works out-of-the-box with smart defaults for your hardware.

### Custom Model Settings (Modelfile)

Create a Modelfile to customize:

```bash
# Create Modelfile
cat > Modelfile << 'EOF'
FROM llama3.1

# Adjust temperature
PARAMETER temperature 0.8

# Set context window
PARAMETER num_ctx 8192

# GPU layers (auto-detected, but can override)
PARAMETER num_gpu 41

# Custom system prompt
SYSTEM You are a helpful AI assistant specialized in coding.
EOF

# Create custom model
docker exec -i ollama ollama create my-llama < Modelfile

# Run your custom model
docker exec -it ollama ollama run my-llama
```

### Environment Variables

Edit `docker-compose.yml`:

```yaml
environment:
  # Number of parallel requests
  - OLLAMA_NUM_PARALLEL=4

  # Max loaded models
  - OLLAMA_MAX_LOADED_MODELS=2

  # Keep models in memory
  - OLLAMA_KEEP_ALIVE=5m

  # Enable debug logs
  - OLLAMA_DEBUG=1
```

Restart:
```bash
docker-compose restart
```

## Import Custom GGUF Models

You can import any GGUF model:

```bash
# Download a GGUF model
mkdir -p /workspace/llm/models
cd /workspace/llm/models
wget https://huggingface.co/.../model.gguf

# Create Modelfile
cat > Modelfile << 'EOF'
FROM /models/model.gguf

PARAMETER temperature 0.7
PARAMETER num_ctx 4096
EOF

# Import into Ollama
docker exec -i ollama ollama create my-model < Modelfile
```

## Check GPU Usage

```bash
# View Ollama logs
docker logs -f ollama

# Check NVIDIA GPU
nvidia-smi -l 1

# Should show Ollama using GPU when models are loaded
```

## Model Storage Location

Models are stored in:
```
/workspace/llm/ollama/models/
```

This is persisted via Docker volume.

## Update Ollama

```bash
# Pull latest image
docker-compose pull

# Restart with new image
docker-compose up -d
```

## Troubleshooting

### Model Download Fails

```bash
# Check internet connectivity
docker exec -it ollama ping -c 3 ollama.com

# Try pulling again
docker exec -it ollama ollama pull llama3.1
```

### Out of Memory

```
Error: failed to allocate memory
```

**Fix:**
- Pull smaller model (7B instead of 70B)
- Set `OLLAMA_MAX_LOADED_MODELS=1`
- Reduce `num_ctx` in Modelfile

### Slow Performance

```bash
# Check GPU is being used
docker logs ollama | grep -i gpu

# Should see: "CUDA available: true"
```

If not using GPU:
- Verify NVIDIA runtime in docker-compose.yml
- Check `nvidia-smi` shows GPU

### API Not Responding

```bash
# Check container is running
docker ps | grep ollama

# Check logs for errors
docker logs ollama

# Restart
docker-compose restart
```

### List Models Shows Nothing

```bash
# Pull at least one model first
docker exec -it ollama ollama pull llama3.1

# Then list
docker exec -it ollama ollama list
```

## Commands Reference

```bash
# Pull a model
docker exec -it ollama ollama pull <model>

# List installed models
docker exec -it ollama ollama list

# Run interactive chat
docker exec -it ollama ollama run <model>

# Remove a model
docker exec -it ollama ollama rm <model>

# Show model info
docker exec -it ollama ollama show <model>

# Create custom model from Modelfile
docker exec -i ollama ollama create <name> < Modelfile

# Copy a model
docker exec -it ollama ollama cp <source> <dest>
```

## Performance Tuning

### For Maximum Speed
```yaml
# docker-compose.yml
environment:
  - OLLAMA_NUM_PARALLEL=1  # Single request at a time
  - OLLAMA_MAX_LOADED_MODELS=1  # One model in memory
```

### For Multi-User
```yaml
environment:
  - OLLAMA_NUM_PARALLEL=8  # Handle 8 concurrent requests
  - OLLAMA_MAX_LOADED_MODELS=3  # Keep 3 models loaded
```

### For Development
```yaml
environment:
  - OLLAMA_KEEP_ALIVE=-1  # Never unload models
  - OLLAMA_DEBUG=1  # Verbose logging
```

## Uninstall

```bash
# Stop and remove container
docker-compose down

# Remove models (optional)
rm -rf /workspace/llm/ollama/models
```

## Resources

- [Ollama Library](https://ollama.com/library) - Browse models
- [Ollama GitHub](https://github.com/ollama/ollama)
- [Ollama Documentation](https://github.com/ollama/ollama/tree/main/docs)
- [Model Search](https://ollama.com/search)

## Next Steps

After installation:
1. Pull a model: `docker exec -it ollama ollama pull llama3.1`
2. Test it: `docker exec -it ollama ollama run llama3.1`
3. Try the API (see USAGE.md)
4. Create custom models with Modelfile
