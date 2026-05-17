# Troubleshooting

## Open Design issues

### "Daemon is not running"

Hermes is not in PATH when daemon starts:

```bash
export PATH="$HOME/.local/bin:$PATH"
cd ~/open-design && pnpm tools-dev stop && pnpm tools-dev
```

### BYOK 502 error

Daemon SSRF protection blocking LiteLLM:
1. Use Local CLI mode instead (Hermes must be in PATH)
2. Use `127.0.0.1:4000/v1` as Base URL (not `localhost` or `172.x.x.x`)
3. Test: `curl -s http://127.0.0.1:4000/v1/models | head -1`

### Web UI not opening in Windows browser

Open Design binds to `127.0.0.1` (WSL-only). Use portproxy (Admin PowerShell):

```powershell
$wslIp = (wsl hostname -I).Trim().Split(' ')[0]
$port = 33329  # get actual port from tools-dev output
netsh interface portproxy add v4tov4 listenport=$port listenaddress=127.0.0.1 connectport=$port connectaddress=$wslIp
Start-Process "http://localhost:$port"
```

### Context length exceeded (local models)

Open Design sends ~27K tokens. Local models with 4096 context fail:
- LM Studio: set Context Length to 32768 in server settings
- Ollama qwen3:8b: add `extra_body: {think: false}` to LiteLLM config
- Use `groq-smart` or `nemotron-30b:free` for large redesign tasks

### Generation timeout (30+ min)

Free OpenRouter models (deepseek, gpt-oss) enter patch-loops on large files.
Use `groq-smart` instead – completes in under 60 seconds.

### Model shows empty content `"content":""`

Ollama qwen3 thinking mode filters content via LiteLLM.
Fix in `~/litellm/config.yaml`:

```yaml
- model_name: local-smart
  litellm_params:
    model: ollama/qwen3:8b
    api_base: http://localhost:11434
    extra_body:
      think: false
```

## LiteLLM issues

### LM Studio model not loading

```
Error: LLM Provider NOT provided. Pass in the LLM provider you are trying to call.
```

Wrong model string format:

```yaml
# Wrong
model: google/gemma-4-e4b

# Correct
model: openai/google/gemma-4-e4b
```

### LM Studio unreachable from WSL2

LM Studio runs on Windows. Use Windows host IP (not localhost):

```yaml
api_base: http://172.25.16.1:1234/v1
```

Verify: `curl -s http://172.25.16.1:1234/v1/models | head -1`

## OpenClaw issues

### Gateway start blocked (missing gateway.mode)

```bash
python3 -c "
import json, os
path = os.path.expanduser('~/.openclaw/openclaw.json')
with open(path) as f: cfg = json.load(f)
cfg['gateway']['mode'] = 'local'
cfg['gateway']['port'] = 18789
with open(path, 'w') as f: json.dump(cfg, f, indent=2)
"
openclaw gateway --port 18789
```

### openclaw: command not found

OpenClaw is installed via nvm Node 24:

```bash
export NVM_DIR="$HOME/.nvm" && . "$NVM_DIR/nvm.sh"
which openclaw  # ~/.nvm/versions/node/v24.15.0/bin/openclaw
```

Add to `.bashrc`:
```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

### LM Studio unreachable from OpenClaw

Use Windows host IP during `openclaw configure`:
```
Base URL: http://172.25.16.1:1234
```
NOT `http://localhost:1234`.
