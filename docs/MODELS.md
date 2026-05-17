# Model Guide – vivo2

## LiteLLM aliases (42 available on vivo2)

### Cloud – fast & free (recommended for Open Design)

| Alias | Provider | Context | Best for |
|-------|----------|---------|----------|
| `groq-smart` | Groq / llama-3.3-70b | 131K | Code generation, redesign |
| `groq-fast` | Groq / llama-3.1-8b | 131K | Quick iterations |
| `groq-reason` | Groq / deepseek-r1 | 131K | Complex reasoning |
| `alibaba-smart` | Alibaba / qwen3-235b | 131K | Free, reliable fallback |
| `cloud-free` | OpenRouter free | 131K | Free tier |

### Local – private (WSL2)

| Alias | Backend | Port | Notes |
|-------|---------|------|-------|
| `local-smart` | Ollama qwen3:8b | 11434 | Add `think: false` in config |
| `local-fast` | Ollama qwen2.5:7b | 11434 | Fast, reliable |
| `lmstudio-smart` | LM Studio Gemma 4 E4B | 1234 | `openai/google/gemma-4-e4b`, 172.25.16.1 |
| `local-npu` | GenieAPI NPU | 8912 | llama3.1-8b on Hexagon NPU |

### OpenRouter free (via Hermes Local CLI picker)

| Model | Context | Quality |
|-------|---------|--------|
| `openrouter/nvidia/nemotron-3-nano-30b-a3b:free` | 256K | ⭐⭐⭐⭐ |
| `openrouter/google/gemini-2.5-flash:free` | 1M | ⭐⭐⭐⭐⭐ |
| `openrouter/qwen/qwen3-235b-a22b:free` | 131K | ⭐⭐⭐⭐⭐ |
| `openrouter/openai/gpt-oss-120b:free` | 131K | ⭐⭐⭐⭐ |

## Context limits for Open Design

⚠️ Open Design sends ~27K tokens per request (full project context).
Local models need **≥32K context**:

- `local-smart` (qwen3:8b): 4096 default → **fails**. Fix: `extra_body: {think: false}` in LiteLLM config
- `lmstudio-smart` (gemma-4-e4b): set context to 32768 in LM Studio server settings
- Solution: use `groq-smart` or `nemotron-30b:free` for large redesign tasks

## LM Studio config (critical)

```yaml
# ~/litellm/config.yaml
- model_name: lmstudio-smart
  litellm_params:
    model: openai/google/gemma-4-e4b   # must use openai/ prefix!
    api_base: http://172.25.16.1:1234/v1  # Windows host IP from WSL2
    api_key: lmstudio-local
    max_tokens: 8192
```

## Why BYOK shows 502

The Open Design daemon has SSRF protection:
- ❌ `172.x.x.x` → blocked
- ❌ `localhost` → sometimes blocked (IPv6 resolution)
- ✅ `127.0.0.1:4000/v1` with LiteLLM on `0.0.0.0` → **works**
- ✅ Local CLI mode via Hermes → **no SSRF restrictions**
