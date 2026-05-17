# Open Design + Hermes Integration

Local-first AI design tool integration with Hermes AI gateway on **Snapdragon X Elite (vivo2)** / WSL2.

## Stack

| Component | Location | Port |
|-----------|----------|------|
| Open Design daemon | WSL2 `~/open-design` | dynamic |
| Open Design web UI | WSL2 | dynamic |
| LiteLLM Proxy | WSL2 | 4000 |
| Ollama | WSL2 | 11434 |
| LM Studio | Windows | 1234 |
| Hermes | `C:\Users\istva\AppData\Local\hermes` | — |
| AI Control Center | `C:\AI\apps\control-center` | 5757 |
| OpenClaw gateway | WSL2 | 18789 |

## Architecture

```
Open Design UI (browser)
    │
    ├─► Local CLI mode → Hermes CLI → LiteLLM :4000 → Ollama / LM Studio / OpenRouter
    │
    └─► BYOK mode → http://127.0.0.1:4000/v1 → any model alias
```

## Quick Start

```bash
bash setup.sh
```

## Docs

- [Setup Guide](docs/SETUP.md)
- [Model Guide](docs/MODELS.md)
- [Workflow & Prompts](docs/WORKFLOW.md)
- [Troubleshooting](docs/TROUBLESHOOTING.md)
- [Control Center Integration](control-center/README.md)
- [OpenClaw Integration](openclaw/README.md)
