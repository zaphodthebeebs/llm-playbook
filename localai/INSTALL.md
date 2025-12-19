# LocalAI Installation

## Docker Install

```bash
cd /workspace/llm/localai
docker-compose up -d
```

## Verify

```bash
curl http://localhost:8082/readyz
# Returns: {"status":"ok"}
```

## Install Models

### Method 1: Model Gallery (Easiest)

```bash
# List available models
curl http://localhost:8082/models/available | jq

# Install a model
curl http://localhost:8082/models/apply \
  -H "Content-Type: application/json" \
  -d '{"id": "llama-3.1-8b-instruct"}'
```

### Method 2: Manual GGUF

```bash
# Download GGUF model
mkdir -p /workspace/llm/localai/models
cd /workspace/llm/localai/models
wget https://huggingface.co/.../model.gguf

# Create config
cat > llama-3.yaml << 'EOF'
name: llama-3
parameters:
  model: model.gguf
  temperature: 0.7
  top_k: 40
  top_p: 0.9
context_size: 8192
threads: 8
f16: true
gpu_layers: 35
EOF

# Restart LocalAI
docker-compose restart
```

## Verify Model

```bash
curl http://localhost:8082/v1/models
```

## Configuration

Edit `docker-compose.yml`:
- `THREADS`: CPU threads
- `CONTEXT_SIZE`: Max context
- `DEBUG`: Enable debug logs

## Resources

- [LocalAI Docs](https://localai.io/basics/getting_started/)
- [Model Gallery](https://models.localai.io/)
