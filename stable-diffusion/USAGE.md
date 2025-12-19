# Stable Diffusion WebUI Usage Guide

## Access Web Interface

```
http://localhost:7860
```

## Basic Image Generation

### Text-to-Image (txt2img)

1. Go to "txt2img" tab (default)
2. Enter your prompt
3. Enter negative prompt (optional but recommended)
4. Adjust settings
5. Click "Generate"

**Example:**
- **Prompt**: `a beautiful landscape, mountains, sunset, highly detailed, 4k, masterpiece`
- **Negative**: `ugly, blurry, low quality, watermark, text`
- **Steps**: 20
- **CFG Scale**: 7
- **Size**: 512x512

### Image-to-Image (img2img)

Transform existing images:

1. Go to "img2img" tab
2. Upload or drag image
3. Enter prompt describing desired changes
4. Set "Denoising strength" (0.3-0.7)
   - 0.3 = small changes
   - 0.7 = major changes
5. Generate

**Example Use Cases:**
- Change photo style to painting
- Modify colors/lighting
- Transform sketches to detailed art

## API Usage

### Text-to-Image

```bash
curl -X POST http://localhost:7860/sdapi/v1/txt2img \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "a cyberpunk city at night, neon lights, detailed, 4k",
    "negative_prompt": "blurry, low quality, watermark",
    "steps": 20,
    "width": 512,
    "height": 512,
    "cfg_scale": 7,
    "sampler_name": "DPM++ 2M Karras",
    "batch_size": 1,
    "n_iter": 1
  }'
```

Response:
```json
{
  "images": ["base64_encoded_image"],
  "parameters": {...},
  "info": "..."
}
```

### Image-to-Image

```bash
curl -X POST http://localhost:7860/sdapi/v1/img2img \
  -H "Content-Type: application/json" \
  -d '{
    "init_images": ["base64_encoded_source_image"],
    "prompt": "oil painting style",
    "denoising_strength": 0.5,
    "steps": 20,
    "cfg_scale": 7
  }'
```

### Python Example

```python
import requests
import base64
from PIL import Image
from io import BytesIO

# Text-to-Image
def generate_image(prompt, negative="", steps=20):
    url = "http://localhost:7860/sdapi/v1/txt2img"

    payload = {
        "prompt": prompt,
        "negative_prompt": negative,
        "steps": steps,
        "width": 512,
        "height": 512,
        "cfg_scale": 7,
        "sampler_name": "DPM++ 2M Karras"
    }

    response = requests.post(url, json=payload)
    result = response.json()

    # Decode first image
    image_data = base64.b64decode(result['images'][0])
    image = Image.open(BytesIO(image_data))
    return image

# Generate
img = generate_image(
    prompt="a beautiful sunset over mountains",
    negative="blurry, low quality"
)
img.save("output.png")
```

### Image-to-Image (Python)

```python
def image_to_image(source_path, prompt, strength=0.5):
    url = "http://localhost:7860/sdapi/v1/img2img"

    # Load and encode source image
    with open(source_path, 'rb') as f:
        image_data = base64.b64encode(f.read()).decode()

    payload = {
        "init_images": [image_data],
        "prompt": prompt,
        "denoising_strength": strength,
        "steps": 20,
        "cfg_scale": 7
    }

    response = requests.post(url, json=payload)
    result = response.json()

    # Decode result
    output_data = base64.b64decode(result['images'][0])
    image = Image.open(BytesIO(output_data))
    return image

# Transform
transformed = image_to_image(
    "photo.jpg",
    prompt="oil painting style, vibrant colors",
    strength=0.6
)
transformed.save("painting.png")
```

## Advanced Features

### Using LoRA Models

Add to prompt:
```
a portrait, <lora:detail_tweaker:0.8>, highly detailed
```

Format: `<lora:model_name:weight>`
- Weight 0.0-1.0 (0.8 is typical)

### Prompt Weighting

```
(keyword:1.3)    # Emphasize by 30%
(keyword:0.7)    # De-emphasize by 30%
((keyword))      # Same as (keyword:1.1)
[keyword]        # Same as (keyword:0.9)
```

Example:
```
a (beautiful:1.3) landscape, [fog], (mountains:1.2)
```

### Prompt Alternation

```
[cat|dog]        # Alternates between cat and dog each step
```

### Batch Generation

Web UI:
- Batch count: Generate X separate images
- Batch size: Generate X images simultaneously (uses more VRAM)

API:
```json
{
  "batch_size": 2,  // Simultaneous (faster, uses more VRAM)
  "n_iter": 3       // Sequential (slower, less VRAM)
}
```

### Seed Control

Reproducible results:
```json
{
  "seed": 12345,
  "subseed": -1
}
```

- Same seed + prompt = same image
- -1 = random seed

### Upscaling

1. Generate image at 512x512
2. Go to "Extras" tab
3. Upload generated image
4. Select upscaler (ESRGAN 4x, R-ESRGAN 4x)
5. Scale factor: 2x or 4x
6. Upscale

API:
```bash
curl -X POST http://localhost:7860/sdapi/v1/extra-single-image \
  -H "Content-Type: application/json" \
  -d '{
    "image": "base64_encoded_image",
    "upscaler_1": "R-ESRGAN 4x+",
    "upscaling_resize": 2
  }'
```

### Inpainting

Edit specific parts:

1. img2img → Inpaint
2. Upload image
3. Draw mask over area to change
4. Enter prompt for that area
5. Generate

Use cases:
- Remove objects
- Change backgrounds
- Fix details

## Sampling Methods

| Sampler | Speed | Quality | Use Case |
|---------|-------|---------|----------|
| DPM++ 2M Karras | ⚡⚡⚡ | ⭐⭐⭐⭐⭐ | Best default |
| Euler a | ⚡⚡⚡⚡⚡ | ⭐⭐⭐⭐ | Fast iterations |
| DDIM | ⚡⚡⚡ | ⭐⭐⭐⭐ | Reproducible |
| DPM++ SDE Karras | ⚡⚡ | ⭐⭐⭐⭐⭐ | Best quality |

## Generation Parameters Guide

### Steps (15-30)
- 15-20: Fast, decent quality
- 25-30: Better quality
- 30+: Diminishing returns

### CFG Scale (5-15)
- 5-7: Creative, loose interpretation
- 7-9: Balanced (recommended)
- 10-15: Very literal, can over-saturate

### Denoising Strength (img2img: 0.3-0.8)
- 0.3-0.4: Minor changes
- 0.5-0.6: Moderate changes
- 0.7-0.8: Major transformation

### Resolution

**SD 1.5:**
- Native: 512x512
- Max: 768x768 (direct)
- Higher: Generate at 512, then upscale

**SDXL:**
- Native: 1024x1024
- Max: 1536x1536 (RTX 4090)

## API Endpoints Reference

### List Models
```bash
GET /sdapi/v1/sd-models
```

### Switch Model
```bash
POST /sdapi/v1/options
{
  "sd_model_checkpoint": "model_name.safetensors"
}
```

### Get Progress
```bash
GET /sdapi/v1/progress
```

### Interrupt Generation
```bash
POST /sdapi/v1/interrupt
```

### List Samplers
```bash
GET /sdapi/v1/samplers
```

### List Upscalers
```bash
GET /sdapi/v1/upscalers
```

## Integration Examples

### With n8n

Create workflow:
1. **Webhook** - Receive prompt
2. **HTTP Request** - Call SD API
3. **Code** - Decode base64 image
4. **Store** - Save to file/database
5. **Respond** - Return image URL

### With LocalAI

LocalAI includes SD integration:
```bash
curl http://localhost:8082/v1/images/generations \
  -H "Content-Type: application/json" \
  -d '{
    "model": "stablediffusion",
    "prompt": "a beautiful landscape",
    "size": "512x512"
  }'
```

### With LLM Workflows

1. User asks LLM for image idea
2. LLM generates detailed prompt
3. Send prompt to SD API
4. Return generated image

Example (pseudocode):
```
user_request → LLM → enhanced_prompt → SD API → image
```

## Prompt Engineering

### Structure

Good prompt structure:
```
[Subject], [Style], [Quality modifiers], [Additional details]
```

Example:
```
a dragon, digital art, highly detailed, 4k, sharp focus, trending on artstation, vibrant colors
```

### Quality Modifiers

Add these for better quality:
```
highly detailed, 4k, 8k, uhd, masterpiece, best quality,
sharp focus, professional, trending on artstation
```

### Style Keywords

```
digital art, oil painting, watercolor, pencil sketch,
3d render, photograph, anime, manga, cartoon,
concept art, matte painting, photorealistic
```

### Negative Prompt Template

```
ugly, blurry, low quality, poorly drawn, bad anatomy,
wrong anatomy, extra limb, missing limb, floating limbs,
disconnected limbs, mutation, mutated, ugly, disgusting,
amputation, watermark, text, signature
```

## Common Issues & Solutions

### Blurry Images
- Increase steps to 25-30
- Lower denoising strength (img2img)
- Add "sharp focus, highly detailed" to prompt

### Wrong Colors
- Adjust CFG scale (try 7-9)
- Add color descriptions to prompt
- Check VAE is loaded

### Distorted Faces/Hands
- Use "face" or "hand" in negative prompt
- Enable face restoration (Settings)
- Lower resolution and upscale
- Use ControlNet for better control

### Out of VRAM
- Reduce resolution (512x512)
- Reduce batch size
- Add `--medvram` to launch args
- Use lighter model (SD 1.5 instead of SDXL)

## Tips & Best Practices

1. **Start Simple** - Test with basic prompts first
2. **Use Negative Prompts** - Always include them
3. **Batch Generate** - Make 4-8 images, pick best
4. **Save Good Seeds** - Note seeds of good results
5. **Experiment with Samplers** - Each has different character
6. **Resolution** - Generate at native, upscale after
7. **Model Choice** - Different models for different styles
8. **LoRA Stacking** - Can use multiple LoRAs (keep weight <1.0 total)

## Monitoring

### Check Generation Progress

Web UI shows progress bar.

API:
```bash
# Check progress
curl http://localhost:7860/sdapi/v1/progress

# Returns:
{
  "progress": 0.65,
  "eta_relative": 3.2,
  "state": {...},
  "current_image": "base64..."
}
```

### GPU Usage

```bash
# Monitor GPU
nvidia-smi -l 1

# Should show stable-diffusion using GPU
```

## Resources

- [Prompt Engineering Guide](https://stable-diffusion-art.com/prompt-guide/)
- [Civitai](https://civitai.com/) - Models & inspiration
- [Lexica.art](https://lexica.art/) - Prompt search engine
- [AUTOMATIC1111 Wiki](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki)

## Next Steps

1. Generate your first image
2. Experiment with different prompts
3. Try img2img transformations
4. Download and test different models
5. Integrate with your automation workflows
6. Explore ControlNet for precise control
