# TGI Installation

## Docker Install (Recommended)

```bash
cd /workspace/llm/text-generation-inference
docker-compose up -d
```

## Verify

```bash
curl http://localhost:8080/health
# Returns: {"status":"ready"}
```

## Model Download

TGI automatically downloads models from HuggingFace on first start.

For gated models (Llama, etc.):
```bash
# Set in docker-compose.yml:
environment:
  - HUGGING_FACE_HUB_TOKEN=hf_xxxxxxxxxxxxx
```

## Configuration

Edit `docker-compose.yml`:

```yaml
environment:
  - MODEL_ID=your-model-here
  - MAX_INPUT_LENGTH=4096
  - MAX_TOTAL_TOKENS=8192
  - QUANTIZE=awq  # or gptq, bitsandbytes
```

## Quantization Options

- `awq` - 4-bit, fastest
- `gptq` - 4-bit, good quality
- `bitsandbytes` - 4/8-bit, flexible
- `eetq` - 8-bit, good quality
- Don't set for FP16/BF16

## Troubleshooting

**Out of memory:** Reduce MAX_TOTAL_TOKENS or use quantization
**Model not loading:** Check HF_TOKEN for gated models
**Port conflict:** Change `8080:80` to different port

## Resources

- [TGI Documentation](https://huggingface.co/docs/text-generation-inference/)
- [Supported Models](https://huggingface.co/docs/text-generation-inference/supported_models)
