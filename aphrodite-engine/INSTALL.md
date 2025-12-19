# Aphrodite Engine Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/aphrodite-engine
docker-compose up -d
```

## Verify

```bash
curl http://localhost:8083/v1/models
```

## Install Models

Aphrodite uses HuggingFace models (same as vLLM).

### Method 1: Auto-download (Easiest)

Edit `docker-compose.yml` and set the model:

```yaml
environment:
  - MODEL_NAME=meta-llama/Llama-3.1-70B-Instruct-AWQ
```

Restart:
```bash
docker-compose restart
```

### Method 2: Pre-download Models

```bash
# Install HuggingFace CLI
pip install huggingface-hub

# Login (for gated models like Llama)
huggingface-cli login

# Download model
huggingface-cli download meta-llama/Llama-3.1-70B-Instruct-AWQ \
  --local-dir ~/.cache/huggingface/hub/meta-llama_Llama-3.1-70B-Instruct-AWQ

# Start Aphrodite
docker-compose up -d
```

## Recommended Models for RTX 4090

### 70B Models (Q4 - Best Balance)
```bash
# Llama 3.1 70B AWQ
MODEL_NAME=casperhansen/llama-3.1-70b-instruct-awq

# Qwen 2.5 72B AWQ
MODEL_NAME=Qwen/Qwen2.5-72B-Instruct-AWQ
```

### 8B Models (High Quality)
```bash
# Llama 3.1 8B Full Precision
MODEL_NAME=meta-llama/Llama-3.1-8B-Instruct

# Mistral 7B
MODEL_NAME=mistralai/Mistral-7B-Instruct-v0.3
```

### Community Fine-tunes (Creative Writing)
```bash
# Mythomax (creative writing)
MODEL_NAME=Gryphe/MythoMax-L2-13b

# Toppy (instruction following + creative)
MODEL_NAME=Undi95/Toppy-M-7B
```

## Configuration Options

Edit `docker-compose.yml`:

```yaml
environment:
  - MODEL_NAME=your-model-here
  - QUANTIZATION=awq  # awq, gptq, or omit for FP16
  - MAX_MODEL_LEN=8192  # Context length
  - GPU_MEMORY_UTILIZATION=0.9  # 0.0-1.0
  - MAX_NUM_SEQS=256  # Concurrent requests
```

## Advanced: Multiple Models

To run multiple models, create separate docker-compose files:

```bash
# Copy and modify
cp docker-compose.yml docker-compose.model2.yml

# Edit ports and model name
# Change port 8083 -> 8084
# Change MODEL_NAME

# Start both
docker-compose up -d
docker-compose -f docker-compose.model2.yml up -d
```

## Troubleshooting

### Out of Memory

Reduce GPU memory usage:
```yaml
environment:
  - GPU_MEMORY_UTILIZATION=0.8  # Lower value
  - MAX_MODEL_LEN=4096  # Shorter context
```

### Model Download Fails

```bash
# Check HuggingFace token
docker exec -it aphrodite-engine printenv HF_TOKEN

# Set token in docker-compose.yml
environment:
  - HF_TOKEN=hf_your_token_here
```

### Slow Performance

```bash
# Check GPU is being used
docker logs aphrodite-engine | grep "GPU"

# Should see: "Using GPU device: NVIDIA GeForce RTX 4090"
```

## Monitor Performance

```bash
# View logs
docker logs -f aphrodite-engine

# Check GPU usage
nvidia-smi -l 1
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
rm -rf ~/.cache/huggingface
```

## Resources

- [Aphrodite Engine GitHub](https://github.com/PygmalionAI/aphrodite-engine)
- [vLLM Documentation](https://docs.vllm.ai/)
- [HuggingFace Models](https://huggingface.co/models)
