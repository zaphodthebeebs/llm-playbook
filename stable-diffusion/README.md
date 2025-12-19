# Stable Diffusion WebUI - AI Image Generation

AUTOMATIC1111's Stable Diffusion web interface for generating images from text prompts.

## What is Stable Diffusion?

Stable Diffusion is an open-source AI model that generates images from text descriptions. AUTOMATIC1111's WebUI is the most popular interface for running Stable Diffusion locally, offering extensive features and customization.

## How It Works

```
Text Prompt
  ↓
Stable Diffusion Model
  ├── Text Encoder (CLIP)
  ├── Diffusion Model (UNet)
  └── Image Decoder (VAE)
  ↓
Generated Image
```

## Key Features

### Image Generation
- **Text-to-Image** - Generate images from prompts
- **Image-to-Image** - Transform existing images
- **Inpainting** - Edit specific parts of images
- **Outpainting** - Extend images beyond borders
- **Upscaling** - Increase resolution with AI

### Advanced Features
- **ControlNet** - Precise pose/composition control
- **LoRA Models** - Fine-tuned style adaptations
- **Embeddings** - Custom concepts and styles
- **Extensions** - Huge ecosystem of plugins
- **Batch Processing** - Generate multiple images
- **Prompt Weighting** - Fine-tune prompt emphasis

### Web Interface
- **User-Friendly** - No coding required
- **Settings Panel** - Extensive customization
- **Image Browser** - View generated history
- **PNG Info** - Embedded generation parameters
- **API Access** - RESTful API for automation

## Architecture

```
┌──────────────────────────────────────┐
│    Web UI (Port 7860)                │
│  ├── txt2img                         │
│  ├── img2img                         │
│  ├── Extras (upscale)                │
│  └── Extensions                      │
├──────────────────────────────────────┤
│    AUTOMATIC1111 Backend             │
│  ├── API Server                      │
│  ├── Model Loader                    │
│  └── Generation Pipeline             │
├──────────────────────────────────────┤
│    Stable Diffusion Models           │
│  ├── Checkpoints (.safetensors)     │
│  ├── LoRA (styles)                   │
│  ├── VAE (quality)                   │
│  └── Embeddings (concepts)           │
├──────────────────────────────────────┤
│    GPU Acceleration (CUDA)           │
└──────────────────────────────────────┘
```

## Performance

On RTX 4090 (24GB VRAM):

| Model | Resolution | Time | Batch Size |
|-------|-----------|------|------------|
| SD 1.5 | 512x512 | ~2s | 8-12 |
| SD 1.5 | 768x768 | ~4s | 4-6 |
| SDXL | 1024x1024 | ~6s | 2-4 |
| SDXL | 1536x1536 | ~12s | 1-2 |

*Times per image with 20-30 steps*

## Supported Models

### Checkpoint Models
- **SD 1.5** - Fast, efficient, huge model library
- **SD 2.1** - Improved quality, less popular
- **SDXL** - Best quality, slower, needs more VRAM
- **Community Fine-tunes** - Styles, anime, realism

### Model Types
- **Checkpoints** - Full SD models (.safetensors, .ckpt)
- **LoRA** - Style adaptations (<200MB each)
- **Textual Inversions** - Custom concepts
- **VAE** - Image quality improvements
- **Hypernetworks** - Style modifications

## Quick Example

**Via Web UI:**
1. Open `http://localhost:7860`
2. Enter prompt: `a cyberpunk city at night, neon lights, detailed`
3. Negative prompt: `blurry, low quality, watermark`
4. Click "Generate"

**Via API:**
```bash
curl http://localhost:7860/sdapi/v1/txt2img \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "a beautiful landscape, mountains, sunset",
    "negative_prompt": "ugly, blurry",
    "steps": 20,
    "width": 512,
    "height": 512,
    "cfg_scale": 7
  }'
```

## Common Use Cases

### Creative Work
- Concept art and illustrations
- Character design
- Environment design
- Logo and icon creation

### Content Creation
- Social media graphics
- Blog post images
- Marketing materials
- Product mockups

### Automation
- Bulk image generation
- API integration with workflows
- Automated thumbnail creation
- Dynamic content generation

## vs Other Solutions

| Feature | AUTOMATIC1111 | ComfyUI | Invoke AI |
|---------|--------------|---------|-----------|
| **Ease of Use** | ✅✅✅ | ⚠️ Technical | ✅✅ |
| **Features** | ✅✅✅ | ✅✅✅ | ✅✅ |
| **Speed** | ✅✅ | ✅✅✅ | ✅✅ |
| **Extensions** | ✅✅✅ | ✅ | ✅✅ |
| **API** | ✅ | ⚠️ Limited | ✅ |
| **Web UI** | ✅✅✅ | ⚠️ Node-based | ✅✅✅ |

## Best For

✅ Image generation from text
✅ Creative exploration
✅ Content creation workflows
✅ API-driven image generation
✅ Experimenting with models/styles
✅ Local, private image generation

❌ 3D model generation (use other tools)
❌ Video generation (use other tools)
❌ Maximum speed (use ComfyUI for workflows)

## Popular Extensions

- **ControlNet** - Pose/composition control
- **Deforum** - Animation generation
- **Aspect Ratio Helper** - Quick size presets
- **Dynamic Prompts** - Template-based prompts
- **Image Browser** - Enhanced gallery
- **Regional Prompter** - Different prompts per area

## Model Sources

### Official
- [HuggingFace SD Models](https://huggingface.co/stabilityai)
- [Stability AI](https://stability.ai/)

### Community
- [Civitai](https://civitai.com/) - Largest model library
- [HuggingFace Community](https://huggingface.co/models?other=stable-diffusion)

### Popular Models
- **Realistic Vision** - Photorealistic images
- **DreamShaper** - Versatile, good quality
- **Anything V5** - Anime style
- **Juggernaut XL** - SDXL fine-tune
- **SDXL Base** - Official SDXL

## Hardware Requirements

| Model | VRAM (Min) | VRAM (Recommended) | Speed |
|-------|-----------|-------------------|-------|
| SD 1.5 | 4GB | 8GB | Fast |
| SD 2.1 | 6GB | 10GB | Medium |
| SDXL | 8GB | 12GB | Slower |

Your RTX 4090 (24GB):
- Can run SDXL at 1536x1536
- Multiple SD 1.5 models simultaneously
- High batch sizes for fast generation
- ControlNet + LoRA without issues

## Prompt Engineering Tips

### Good Prompts
- Be specific and descriptive
- Include style keywords (photorealistic, anime, oil painting)
- Mention quality (masterpiece, highly detailed, 4k)
- Describe composition (close-up, wide shot, centered)

### Negative Prompts
Common negatives:
```
ugly, blurry, low quality, watermark, text,
poorly drawn, bad anatomy, wrong anatomy,
extra limb, missing limb, floating limbs
```

### Prompt Weights
```
(keyword:1.2)  - Emphasize keyword by 20%
(keyword:0.8)  - De-emphasize by 20%
((keyword))    - Same as (keyword:1.1)
```

## Generation Parameters

### Important Settings

**Steps** (20-30)
- More steps = better quality
- Diminishing returns after 30
- 20 is good for most cases

**CFG Scale** (7-9)
- How closely to follow prompt
- 7 = balanced
- Higher = more literal, can over-saturate

**Sampler**
- DPM++ 2M Karras - Good default
- Euler a - Fast, good for iterations
- DDIM - Consistent, good for img2img

**Seed**
- -1 = random
- Set number for reproducibility

## Integration with LLMs

Generate images from LLM descriptions:

**Via n8n:**
1. LLM generates image description
2. HTTP Request to SD API
3. Return generated image

**Via LocalAI:**
- LocalAI has built-in SD support
- Use `/v1/images/generations` endpoint

## API Endpoints

**Text-to-Image:**
```
POST /sdapi/v1/txt2img
```

**Image-to-Image:**
```
POST /sdapi/v1/img2img
```

**List Models:**
```
GET /sdapi/v1/sd-models
```

**Get Options:**
```
GET /sdapi/v1/options
```

**Progress:**
```
GET /sdapi/v1/progress
```

## Tips for Quality

1. **Good Models** - Download quality checkpoints
2. **Appropriate Steps** - 20-30 for most cases
3. **Negative Prompts** - Always use them
4. **CFG Scale** - 7-9 range
5. **Resolution** - Start with native (512x512 for SD1.5, 1024x1024 for SDXL)
6. **Batch Testing** - Generate multiple, pick best
7. **Upscale** - Use extras tab to upscale results

## Fun Fact

AUTOMATIC1111's Stable Diffusion WebUI is the most popular SD interface, with over 100k GitHub stars. It's become the standard for local SD deployment, with a massive extension ecosystem and active community. The developer (AUTOMATIC1111) chose to remain pseudonymous!
