# Stable Diffusion WebUI Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/stable-diffusion
docker-compose up -d
```

## First Start (Downloads Models)

The first start will take 10-15 minutes to download the default SD 1.5 model (~4GB).

```bash
# Watch download progress
docker logs -f stable-diffusion

# Wait for: "Running on local URL:  http://0.0.0.0:7860"
```

## Access Web UI

Open browser to:
```
http://localhost:7860
```

## Verify Installation

```bash
# Check container is running
docker ps | grep stable-diffusion

# Check GPU is detected
docker logs stable-diffusion | grep "GPU"

# Test API
curl http://localhost:7860/sdapi/v1/sd-models
```

## Download Models

### Method 1: Via Web UI

1. Open `http://localhost:7860`
2. Click "Checkpoint" dropdown (top left)
3. Click refresh button
4. Select model from list

### Method 2: Manual Download (Faster)

Download models to `/workspace/llm/models/stable-diffusion/`

#### SD 1.5 Models (Recommended to Start)

```bash
cd /workspace/llm/models/stable-diffusion

# Realistic Vision (photorealistic)
wget -O realistic_vision_v5.safetensors \
  https://civitai.com/api/download/models/130072

# DreamShaper (versatile)
wget -O dreamshaper_8.safetensors \
  https://civitai.com/api/download/models/128713

# Or use HuggingFace
wget https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.safetensors
```

#### SDXL Models (Better Quality, Slower)

```bash
# SDXL Base (official)
wget -O sdxl_base_1.0.safetensors \
  https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0/resolve/main/sd_xl_base_1.0.safetensors

# Juggernaut XL (popular fine-tune)
wget -O juggernautXL_v9.safetensors \
  https://civitai.com/api/download/models/456194
```

### Method 3: From Civitai

1. Visit [Civitai.com](https://civitai.com/)
2. Browse models
3. Download `.safetensors` file
4. Place in `/workspace/llm/models/stable-diffusion/`
5. Refresh model list in web UI

## Download LoRA Models (Optional)

LoRA models add styles/concepts:

```bash
cd /workspace/llm/models/stable-diffusion/lora

# Example: Detail enhancer
wget -O detail_tweaker.safetensors \
  https://civitai.com/api/download/models/135867
```

Use in prompts: `<lora:detail_tweaker:1.0>`

## Download VAE (Improves Quality)

```bash
cd /workspace/llm/models/stable-diffusion/vae

# SD 1.5 VAE
wget https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.safetensors

# SDXL VAE
wget https://huggingface.co/stabilityai/sdxl-vae/resolve/main/sdxl_vae.safetensors
```

Set in web UI: Settings → VAE → Select VAE

## Install Extensions

### Via Web UI

1. Go to "Extensions" tab
2. Click "Available" sub-tab
3. Click "Load from"
4. Search for extension
5. Click "Install"
6. Restart required

### Popular Extensions

**ControlNet** (pose/composition control):
1. Extensions → Install from URL
2. URL: `https://github.com/Mikubill/sd-webui-controlnet`
3. Install
4. Restart

**Image Browser** (better gallery):
```
https://github.com/AlUlkesh/stable-diffusion-webui-images-browser
```

**Dynamic Prompts** (templates):
```
https://github.com/adieyal/sd-dynamic-prompts
```

## Configuration

### Optimize for RTX 4090

Edit `docker-compose.yml`:

```yaml
environment:
  - CLI_ARGS=--listen --api --xformers --enable-insecure-extension-access --no-half-vae
```

Flags explained:
- `--xformers` - Memory efficient attention (faster)
- `--no-half-vae` - Better quality VAE
- `--medvram` - Use if you run out of VRAM
- `--lowvram` - For <8GB VRAM (not needed for 4090)

Restart:
```bash
docker-compose restart
```

### API Configuration

API is enabled by default with `--api` flag.

Test API:
```bash
curl http://localhost:7860/sdapi/v1/sd-models
```

### Settings (In Web UI)

**Recommended Settings:**
1. Settings → User Interface
   - Quicksettings: `sd_model_checkpoint, CLIP_stop_at_last_layers`
2. Settings → Stable Diffusion
   - VAE: Select your VAE
   - CLIP skip: 2 (for anime models)
3. Settings → System
   - Auto-save images: Yes
   - Grid size: 4x4

## Model Storage Structure

```
/workspace/llm/models/stable-diffusion/
├── model.safetensors          # Checkpoints (SD models)
├── another-model.safetensors
├── lora/                      # LoRA models
│   └── style.safetensors
├── vae/                       # VAE models
│   └── vae.safetensors
├── embeddings/                # Textual inversions
│   └── concept.pt
└── controlnet/                # ControlNet models
    └── control.pth
```

## Download ControlNet Models (Optional)

For pose control:

```bash
cd /workspace/llm/models/stable-diffusion/controlnet

# OpenPose (human pose)
wget https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_openpose.pth

# Canny (edge detection)
wget https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_canny.pth
```

## Troubleshooting

### WebUI Won't Start

```bash
# Check logs
docker logs stable-diffusion

# Common issues:
# - GPU not detected
# - Model download failed
# - Port 7860 already in use
```

### Out of VRAM

Reduce resolution or batch size:
- Use 512x512 instead of 768x768
- Reduce batch size from 4 to 2
- Add `--medvram` to CLI_ARGS

### Slow Generation

```bash
# Check GPU is being used
nvidia-smi

# Should show stable-diffusion using GPU
# If not, check docker GPU passthrough
```

### Can't Download Models

```bash
# Manual download to host
cd /workspace/llm/models/stable-diffusion

# Then restart
docker-compose restart
```

### Extension Install Failed

Some extensions need git:
```bash
# Enter container
docker exec -it stable-diffusion bash

# Install git (if needed)
apt-get update && apt-get install -y git

# Then install extension via web UI
```

## Update

```bash
# Pull latest WebUI
docker-compose pull

# Restart
docker-compose up -d
```

## Uninstall

```bash
# Stop and remove container
docker-compose down

# Remove models (optional - they're large!)
rm -rf /workspace/llm/models/stable-diffusion
```

## Recommended Starter Pack

For best experience, download:

1. **Model**: Realistic Vision v5 or DreamShaper
2. **VAE**: sd-vae-ft-mse
3. **Extension**: ControlNet
4. **Extension**: Image Browser

Total download: ~6-8GB

## Performance Benchmarks

On RTX 4090:

**SD 1.5 @ 512x512:**
- 20 steps: ~2 seconds
- Batch of 4: ~6 seconds

**SDXL @ 1024x1024:**
- 25 steps: ~7 seconds
- Batch of 2: ~12 seconds

## Resources

- [AUTOMATIC1111 Wiki](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki)
- [Civitai Models](https://civitai.com/)
- [HuggingFace SD Models](https://huggingface.co/models?other=stable-diffusion)
- [ControlNet Models](https://huggingface.co/lllyasviel/ControlNet-v1-1)

## Next Steps

1. Generate your first image
2. Experiment with different prompts
3. Try different models
4. Install ControlNet for pose control
5. Integrate with n8n workflows
