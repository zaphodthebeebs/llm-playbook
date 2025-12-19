# Ollama Usage

## Command Line Interface

Ollama provides a simple CLI for interactive use.

### Interactive Chat

```bash
# Run a model in chat mode
docker exec -it ollama ollama run llama3.1

# Chat with the model
>>> Hello! How are you?
[AI responds...]

>>> Can you write me a Python function?
[AI responds...]

# Exit
>>> /bye
```

### Special Commands in Chat

- `/bye` - Exit the chat
- `/clear` - Clear conversation history
- `/show` - Show model info
- `/set parameter value` - Change parameters
- `/help` - Show help

### One-Shot Generation

```bash
# Generate a single response
docker exec ollama ollama run llama3.1 "Write a haiku about AI"

# With a file as input
cat prompt.txt | docker exec -i ollama ollama run llama3.1
```

## REST API

Ollama provides OpenAI-compatible and native APIs.

### Generate (Native API)

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

### Chat (Native API)

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.1",
  "messages": [
    {
      "role": "user",
      "content": "Hello! Tell me about yourself."
    }
  ],
  "stream": false
}'
```

### Streaming Response

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.1",
  "messages": [
    {"role": "user", "content": "Write a story"}
  ],
  "stream": true
}'
```

### With Parameters

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1",
  "prompt": "Write creative fiction",
  "stream": false,
  "options": {
    "temperature": 0.9,
    "top_p": 0.95,
    "top_k": 40,
    "num_ctx": 8192
  }
}'
```

## OpenAI-Compatible API

Ollama provides OpenAI-compatible endpoints for easy integration.

### Chat Completion

```bash
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "What is machine learning?"}
    ],
    "temperature": 0.7,
    "max_tokens": 500
  }'
```

### Completion

```bash
curl http://localhost:11434/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1",
    "prompt": "The meaning of life is",
    "max_tokens": 100,
    "temperature": 0.7
  }'
```

### List Models

```bash
curl http://localhost:11434/v1/models
```

## Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama"  # required but ignored
)

# Chat completion
response = client.chat.completions.create(
    model="llama3.1",
    messages=[
        {"role": "system", "content": "You are a helpful coding assistant."},
        {"role": "user", "content": "Write a Python function to reverse a string"}
    ],
    temperature=0.7
)
print(response.choices[0].message.content)

# Streaming
stream = client.chat.completions.create(
    model="llama3.1",
    messages=[{"role": "user", "content": "Tell me a story"}],
    stream=True
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end='', flush=True)
```

## Python (Native Ollama Library)

```bash
# Install ollama-python
pip install ollama
```

```python
import ollama

# Simple generation
response = ollama.generate(
    model='llama3.1',
    prompt='Why is the ocean salty?'
)
print(response['response'])

# Chat
response = ollama.chat(
    model='llama3.1',
    messages=[
        {'role': 'user', 'content': 'Hello!'}
    ]
)
print(response['message']['content'])

# Streaming
for chunk in ollama.chat(
    model='llama3.1',
    messages=[{'role': 'user', 'content': 'Write a poem'}],
    stream=True
):
    print(chunk['message']['content'], end='', flush=True)
```

## Vision Models

```python
import ollama

# Chat with image
response = ollama.chat(
    model='llama3.2-vision',
    messages=[
        {
            'role': 'user',
            'content': 'What is in this image?',
            'images': ['./image.jpg']
        }
    ]
)
print(response['message']['content'])

# With URL
response = ollama.chat(
    model='llava',
    messages=[
        {
            'role': 'user',
            'content': 'Describe this image',
            'images': ['https://example.com/image.jpg']
        }
    ]
)
```

## Model Management API

### List Models

```bash
curl http://localhost:11434/api/tags
```

### Show Model Info

```bash
curl http://localhost:11434/api/show -d '{
  "name": "llama3.1"
}'
```

### Pull Model

```bash
curl http://localhost:11434/api/pull -d '{
  "name": "llama3.1:70b"
}'
```

### Delete Model

```bash
curl -X DELETE http://localhost:11434/api/delete -d '{
  "name": "llama3.1"
}'
```

### Copy Model

```bash
curl http://localhost:11434/api/copy -d '{
  "source": "llama3.1",
  "destination": "my-llama"
}'
```

## Advanced: Embeddings

```bash
curl http://localhost:11434/api/embeddings -d '{
  "model": "llama3.1",
  "prompt": "The quick brown fox"
}'
```

```python
import ollama

# Generate embeddings
embeddings = ollama.embeddings(
    model='llama3.1',
    prompt='Text to embed'
)
print(embeddings['embedding'])
```

## Parameter Reference

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `temperature` | float | 0.8 | Randomness (0.0-2.0) |
| `top_p` | float | 0.9 | Nucleus sampling |
| `top_k` | int | 40 | Top-k sampling |
| `num_ctx` | int | 2048 | Context window size |
| `num_predict` | int | 128 | Max tokens to generate |
| `repeat_penalty` | float | 1.1 | Repetition penalty |
| `stop` | array | - | Stop sequences |
| `num_gpu` | int | auto | GPU layers to offload |
| `num_thread` | int | auto | CPU threads |

## Usage Examples

### Code Generation

```python
response = ollama.chat(
    model='codellama',
    messages=[
        {
            'role': 'system',
            'content': 'You are an expert Python programmer.'
        },
        {
            'role': 'user',
            'content': 'Write a binary search function'
        }
    ]
)
```

### Creative Writing

```python
response = ollama.generate(
    model='llama3.1',
    prompt='Write a short story about time travel',
    options={
        'temperature': 0.9,
        'top_p': 0.95,
        'num_predict': 500
    }
)
```

### Q&A with Context

```python
messages = [
    {'role': 'system', 'content': 'You are a helpful assistant.'},
    {'role': 'user', 'content': 'What is Python?'},
    {'role': 'assistant', 'content': 'Python is a high-level programming language...'},
    {'role': 'user', 'content': 'What are its main features?'}
]

response = ollama.chat(model='llama3.1', messages=messages)
```

### JSON Mode

```python
response = ollama.chat(
    model='llama3.1',
    messages=[
        {
            'role': 'user',
            'content': 'List 3 colors in JSON format'
        }
    ],
    format='json'
)
print(response['message']['content'])
# Output: {"colors": ["red", "blue", "green"]}
```

## Integration Examples

### LangChain

```python
from langchain_community.llms import Ollama

llm = Ollama(
    model="llama3.1",
    base_url="http://localhost:11434"
)

response = llm.invoke("What is the capital of France?")
print(response)
```

### LlamaIndex

```python
from llama_index.llms.ollama import Ollama

llm = Ollama(
    model="llama3.1",
    base_url="http://localhost:11434"
)

response = llm.complete("Explain quantum computing")
print(response)
```

## Tips & Best Practices

### For Best Quality
- Use larger models (70B) when accuracy matters
- Lower temperature (0.3-0.5) for factual responses
- Increase context window (`num_ctx`) for long conversations
- Use system prompts to guide behavior

### For Speed
- Use smaller models (7-8B) for simple tasks
- Reduce `num_ctx` to 2048
- Set `OLLAMA_NUM_PARALLEL=1` for single-user
- Keep models loaded with `OLLAMA_KEEP_ALIVE=-1`

### For Creative Tasks
- Increase temperature (0.8-1.0)
- Use higher `top_p` (0.95)
- Increase `num_predict` for longer outputs
- Try specialized models (storytelling fine-tunes)

### For Code
- Use `codellama` or `deepseek-coder` models
- Lower temperature (0.2-0.4)
- Clear, specific prompts
- Use system prompt to specify language

## Monitor Performance

```bash
# View real-time logs
docker logs -f ollama

# Check GPU usage
nvidia-smi -l 1

# Check memory usage
docker stats ollama
```

## Troubleshooting

### Model Not Found
```bash
# List available models
docker exec -it ollama ollama list

# Pull the model
docker exec -it ollama ollama pull llama3.1
```

### Slow Response
- Use smaller model
- Reduce `num_ctx`
- Check GPU is being used (`nvidia-smi`)
- Increase GPU layers in Modelfile

### Out of Memory
- Use smaller model (7B instead of 70B)
- Reduce context window
- Set `OLLAMA_MAX_LOADED_MODELS=1`

## Resources

- [Ollama API Docs](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Ollama Python Library](https://github.com/ollama/ollama-python)
- [OpenAI Python SDK](https://github.com/openai/openai-python)
- [Model Library](https://ollama.com/library)
