# vLLM Installation Guide

## Prerequisites

- NVIDIA GPU with CUDA support (RTX 4090 ✅)
- Docker with NVIDIA Container Toolkit
- At least 50GB free disk space for models

## Installation Methods

### Method 1: Docker (Recommended)

Use the provided `docker-compose.yml`:

```bash
cd /workspace/llm/vllm
docker-compose up -d
```

That's it! vLLM will:
1. Download the Docker image
2. Pull the model from HuggingFace
3. Start serving on port 8000

### Method 2: Manual Docker

```bash
docker run --runtime nvidia --gpus all \
  -v ~/.cache/huggingface:/root/.cache/huggingface \
  -p 8000:8000 \
  --ipc=host \
  vllm/vllm-openai:latest \
  --model meta-llama/Llama-3.1-70B-Instruct \
  --dtype auto \
  --max-model-len 8192
```

### Method 3: pip Install (Advanced)

```bash
# Create virtual environment
python3 -m venv vllm-env
source vllm-env/bin/activate

# Install vLLM with CUDA support
pip install vllm

# For latest features
pip install git+https://github.com/vllm-project/vllm.git

# Start server
python -m vllm.entrypoints.openai.api_server \
  --model meta-llama/Llama-3.1-70B-Instruct \
  --port 8000
```

## Verify Installation

```bash
# Check if server is running
curl http://localhost:8000/v1/models

# Should return:
# {
#   "object": "list",
#   "data": [{
#     "id": "meta-llama/Llama-3.1-70B-Instruct",
#     "object": "model",
#     "created": 1234567890,
#     "owned_by": "vllm"
#   }]
# }
```

## Model Downloads

### Automatic Download (Recommended)

vLLM automatically downloads models from HuggingFace on first run.

Models are cached in: `~/.cache/huggingface/`

### Manual Download

```bash
# Install huggingface-cli
pip install huggingface-hub

# Login (for gated models like Llama)
huggingface-cli login

# Download model
huggingface-cli download meta-llama/Llama-3.1-70B-Instruct

# Or use git
git lfs install
git clone https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct
```

### Pre-download Models for Docker

```bash
# Download before starting Docker
docker run --rm \
  -v ~/.cache/huggingface:/root/.cache/huggingface \
  vllm/vllm-openai:latest \
  python -c "from transformers import AutoTokenizer; AutoTokenizer.from_pretrained('meta-llama/Llama-3.1-70B-Instruct')"
```

## Configuration Options

### Environment Variables

```bash
# HuggingFace token (for gated models)
export HF_TOKEN=hf_xxxxxxxxxxxx

# Cache directory
export HF_HOME=/path/to/cache

# CUDA devices
export CUDA_VISIBLE_DEVICES=0  # Use GPU 0 only
```

### Common Parameters

```bash
--model MODEL_NAME              # HuggingFace model ID or local path
--tensor-parallel-size N        # Number of GPUs for tensor parallelism
--dtype auto|float16|bfloat16   # Model precision
--max-model-len LENGTH          # Max sequence length
--gpu-memory-utilization FRAC   # GPU memory to use (0.0-1.0)
--max-num-seqs NUM              # Max concurrent sequences
--port PORT                     # Server port
--host HOST                     # Server host
```

## Recommended Models for RTX 4090

### 70B Models (Quantized)

```bash
# AWQ 4-bit (fastest, 24GB VRAM)
--model TheBloke/Llama-2-70B-Chat-AWQ

# GPTQ 4-bit (better quality, 24GB VRAM)
--model TheBloke/Llama-2-70B-Chat-GPTQ
```

### 34B Models (Full Precision)

```bash
# Full FP16 (20GB VRAM)
--model codellama/CodeLlama-34b-Instruct-hf

# Yi-34B (excellent quality, 20GB VRAM)
--model 01-ai/Yi-34B-Chat
```

### 13B Models (Full Precision)

```bash
# Llama 3.1 13B
--model meta-llama/Llama-3.1-13B-Instruct

# Mistral 7B (very efficient)
--model mistralai/Mistral-7B-Instruct-v0.3
```

## Performance Tuning

### For Maximum Throughput

```bash
vllm serve MODEL \
  --max-num-seqs 256 \
  --gpu-memory-utilization 0.95 \
  --max-model-len 4096 \
  --enforce-eager  # Skip CUDA graph compilation
```

### For Low Latency

```bash
vllm serve MODEL \
  --max-num-seqs 32 \
  --max-model-len 2048 \
  --gpu-memory-utilization 0.8
```

### For Long Context

```bash
vllm serve MODEL \
  --max-model-len 32768 \
  --gpu-memory-utilization 0.9 \
  --enable-prefix-caching  # Speeds up repeated prefixes
```

## Troubleshooting

### Issue: Out of Memory

**Solution 1:** Reduce max model length
```bash
--max-model-len 4096  # instead of 8192
```

**Solution 2:** Reduce GPU memory utilization
```bash
--gpu-memory-utilization 0.8  # instead of 0.9
```

**Solution 3:** Use quantized models
```bash
--model TheBloke/Llama-2-70B-Chat-AWQ  # AWQ 4-bit
```

### Issue: CUDA Error

**Check CUDA installation:**
```bash
nvidia-smi
nvcc --version
```

**Update NVIDIA drivers:**
```bash
# Ubuntu
sudo apt update
sudo apt install nvidia-driver-535  # or latest

# Restart required
sudo reboot
```

### Issue: Model Download Fails

**Use HuggingFace token:**
```bash
# For gated models (Llama, etc.)
export HF_TOKEN=hf_xxxxxxxxxxxxx

# Or pass directly
--huggingface-token hf_xxxxxxxxxxxxx
```

### Issue: Slow First Token

**Enable prefix caching:**
```bash
--enable-prefix-caching
```

**Pre-compile CUDA graphs:**
```bash
# Remove --enforce-eager if set
# Let vLLM compile graphs on startup (slower start, faster inference)
```

## Updating vLLM

### Docker

```bash
docker pull vllm/vllm-openai:latest
docker-compose down
docker-compose up -d
```

### pip

```bash
pip install --upgrade vllm
```

## Uninstallation

### Docker

```bash
docker-compose down
docker rmi vllm/vllm-openai:latest
```

### pip

```bash
pip uninstall vllm
rm -rf ~/.cache/huggingface  # Remove cached models
```

## Next Steps

After installation:
1. Read [USAGE.md](./USAGE.md) for API examples
2. Test with `curl http://localhost:8000/v1/models`
3. Try the example requests in USAGE.md
4. Monitor with `docker logs -f vllm`

## Support Resources

- [vLLM Documentation](https://docs.vllm.ai/)
- [GitHub Issues](https://github.com/vllm-project/vllm/issues)
- [Discord Community](https://discord.gg/vllm)
- [Model Compatibility List](https://docs.vllm.ai/en/latest/models/supported_models.html)
