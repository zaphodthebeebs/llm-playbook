# KoboldCpp Usage

## Web Interface

KoboldCpp is primarily used through its web interface.

### Access

```
http://localhost:5001
```

## Getting Started

### 1. Load a Model

1. Click the **AI icon** (top-left) or **"Load Model"**
2. Select or enter model path:
   ```
   /models/llama-2-70b-chat.Q4_K_M.gguf
   ```
3. Configure settings:
   - **GPU Layers**: 41 (for RTX 4090)
   - **Threads**: 8
   - **Context Size**: 4096
4. Click **"Load"**
5. Wait for "Model loaded successfully"

### 2. Choose a Mode

Click **"New Story"** and select:

- **Chat Mode** - Chatbot conversation
- **Story Mode** - Collaborative writing
- **Adventure Mode** - Interactive fiction (you type actions)
- **Instruct Mode** - Direct instructions

### 3. Start Generating

- **Type your prompt** in the text box
- Click **"Submit"** or **"Generate"**
- Watch as text generates in real-time
- Click **"Generate"** again to continue

## Interface Overview

### Top Bar
- **AI Icon**: Load/change model
- **Settings Icon**: Adjust generation parameters
- **Save Icon**: Save your session
- **Load Icon**: Load saved session

### Main Panel
- **Story Text**: Your generated content
- **Input Box**: Type your prompt/response
- **Submit**: Send your input
- **Generate**: Continue generation
- **Retry**: Regenerate last output

### Right Panel (Settings)
- **Amount to Generate**: Max tokens (50-500)
- **Temperature**: Randomness (0.1-2.0)
- **Top P**: Nucleus sampling
- **Top K**: Top-k sampling
- **Typical**: Typical sampling
- **Repetition Penalty**: Reduce repetition
- **Memory**: Permanent context
- **Author's Note**: Generation guide

## Generation Settings

### Temperature
Controls randomness:
- **0.3-0.5**: Focused, predictable
- **0.7-0.8**: Balanced (recommended)
- **0.9-1.2**: Creative, varied
- **1.5+**: Very random, experimental

### Top P (Nucleus Sampling)
Limits token pool:
- **0.9**: Conservative
- **0.95**: Balanced (recommended)
- **1.0**: All tokens considered

### Repetition Penalty
Reduces repetitive text:
- **1.0**: No penalty
- **1.1**: Slight penalty (recommended)
- **1.2-1.3**: Strong penalty (dialogue)
- **1.5+**: May cause incoherence

### Amount to Generate
Tokens per generation:
- **50-100**: Short responses
- **150-200**: Medium paragraphs
- **300-500**: Long outputs

## Advanced Features

### Memory

Permanent context that's always remembered:

1. Click **"Memory"** button
2. Enter character details, world info, style:
   ```
   You are a wise wizard named Gandalf.
   The story is set in Middle Earth.
   Write in epic fantasy style.
   ```
3. This context is prepended to every generation

### Author's Note

Guide the AI's next output:

1. Click **"A/N"** button
2. Enter what should happen next:
   ```
   [The dragon reveals its true identity]
   ```
3. Injected right before generation

### World Info

Context activated by keywords:

1. Click **"World Info"**
2. Add entries:
   - **Keys**: `dragon, Smaug`
   - **Content**: `Smaug is an ancient red dragon who hoards gold.`
3. Auto-injected when keywords appear

### Save/Load

Save your session:
1. Click **Save icon**
2. Enter filename: `my-story.json`
3. Download saves to browser

Load session:
1. Click **Load icon**
2. Select `.json` file
3. Story and settings restored

## Presets

### Balanced Writing
- Temperature: **0.7**
- Top P: **0.9**
- Repetition Penalty: **1.1**
- Amount: **200**

### Creative/Experimental
- Temperature: **1.0**
- Top P: **0.95**
- Repetition Penalty: **1.15**
- Top K: **100**
- Amount: **300**

### Focused/Coherent
- Temperature: **0.5**
- Top P: **0.85**
- Repetition Penalty: **1.2**
- Typical: **0.9**
- Amount: **150**

### Dialogue
- Temperature: **0.8**
- Top P: **0.9**
- Repetition Penalty: **1.25**
- Amount: **100**

## API Usage (Optional)

KoboldCpp also provides API endpoints.

### OpenAI-Compatible API

```bash
curl http://localhost:5001/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "koboldcpp",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Tell me a story"}
    ],
    "temperature": 0.7,
    "max_tokens": 200
  }'
```

### Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:5001/v1",
    api_key="dummy"
)

response = client.chat.completions.create(
    model="koboldcpp",
    messages=[
        {"role": "system", "content": "You are a creative storyteller."},
        {"role": "user", "content": "Write a short fantasy story."}
    ],
    temperature=0.8,
    max_tokens=500
)
print(response.choices[0].message.content)
```

### KoboldAI API (Native)

```bash
curl http://localhost:5001/api/v1/generate \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Once upon a time in a land far away",
    "max_length": 200,
    "temperature": 0.7,
    "top_p": 0.9,
    "rep_pen": 1.1
  }'
```

### Python (Native API)

```python
import requests

response = requests.post(
    "http://localhost:5001/api/v1/generate",
    json={
        "prompt": "The dragon spread its wings and",
        "max_length": 300,
        "temperature": 0.8,
        "top_p": 0.95,
        "rep_pen": 1.15,
        "top_k": 100
    }
)

print(response.json()["results"][0]["text"])
```

### Check Model Info

```bash
curl http://localhost:5001/api/v1/model
```

Returns:
```json
{
  "result": "llama-2-70b-chat.Q4_K_M",
  "version": "1.65"
}
```

## Tips & Tricks

### For Best Quality
1. Use **higher quantization** (Q5/Q6) if VRAM allows
2. Set **context size to 8192** for long conversations
3. Use **Memory** for character consistency
4. Adjust **Repetition Penalty** per model (some need more)

### For Speed
1. Reduce **Amount to Generate** (100-150)
2. Lower **context size** to 2048
3. Use **Q4 quantization**
4. Increase **GPU layers** to maximum

### For Creative Writing
1. Temperature: **0.8-1.0**
2. Use **Author's Note** to guide plot
3. Use **World Info** for consistent lore
4. **Save frequently** (every few scenes)

### For Chatbots
1. Temperature: **0.7-0.8**
2. Repetition Penalty: **1.2-1.3**
3. Use **Memory** for character personality
4. Shorter **Amount to Generate** (100-150)

### For Role-Playing
1. Temperature: **0.8-0.9**
2. Use **Memory** for character card
3. Use **World Info** for game mechanics
4. **Adventure Mode** for interactive play

## Keyboard Shortcuts

- **Enter**: Submit input
- **Ctrl+Enter**: Generate more
- **Ctrl+Z**: Undo last generation
- **Ctrl+S**: Save story

## Monitor Performance

Check Docker logs for performance stats:
```bash
docker logs -f koboldcpp
```

Look for:
- `Processing time: X ms`
- `Tokens per second: Y`
- `Context usage: Z / 4096`

Check GPU usage:
```bash
nvidia-smi -l 1
```

## Troubleshooting

### Generation is slow
- Increase GPU layers
- Reduce context size
- Use smaller model or lower quantization

### Repetitive output
- Increase Repetition Penalty (1.2-1.3)
- Increase Frequency Penalty
- Try different Temperature

### Incoherent output
- Lower Temperature (0.6-0.7)
- Reduce Top K
- Check Repetition Penalty isn't too high

### Out of memory
- Reduce context size (2048 or 4096)
- Reduce GPU layers
- Use smaller model

## Community Resources

- [KoboldAI Discord](https://discord.gg/koboldai)
- [Character Cards](https://www.characterhub.org/)
- [Presets & Settings](https://github.com/KoboldAI/KoboldAI-Client/wiki/Settings-Presets)
- [Model Recommendations](https://www.reddit.com/r/KoboldAI/)

## Fun Use Cases

### Interactive Fiction
Create "choose your own adventure" games in Adventure Mode

### Collaborative Stories
Take turns with the AI writing chapters

### Character Chat
Have conversations with your favorite fictional characters

### World Building
Generate lore, histories, and details for RPG campaigns

### Writing Assistant
Brainstorm plot ideas, overcome writer's block
