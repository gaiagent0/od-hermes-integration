# OpenClaw Integration

OpenClaw (v2026.5.12) – Crestodian agent framework with WebSocket gateway.

## Installation path on vivo2

```
~/.nvm/versions/node/v24.15.0/bin/openclaw
```

Always load nvm before using:

```bash
export NVM_DIR="$HOME/.nvm" && . "$NVM_DIR/nvm.sh"
openclaw --version
```

## Initial setup

```bash
# Fix gateway.mode config
python3 -c "
import json, os
path = os.path.expanduser('~/.openclaw/openclaw.json')
with open(path) as f: cfg = json.load(f)
cfg['gateway']['mode'] = 'local'
cfg['gateway']['port'] = 18789
with open(path, 'w') as f: json.dump(cfg, f, indent=2)
"

# Configure model (LM Studio on Windows host)
openclaw configure
# Provider: LM Studio
# Base URL: http://172.25.16.1:1234  (NOT localhost!)
# Context: 32768

# Start gateway
openclaw gateway --port 18789
```

## Gateway (background)

```bash
export NVM_DIR="$HOME/.nvm" && . "$NVM_DIR/nvm.sh"
nohup openclaw gateway --port 18789 > /tmp/openclaw-gateway.log 2>&1 &
```

## Useful commands

```bash
openclaw chat          # Local terminal UI
openclaw status        # Channel health
openclaw health        # Gateway health
openclaw logs          # Tail gateway logs
openclaw doctor        # Health checks + fixes
openclaw models status # Model status
openclaw crestodian    # Setup/repair helper
```

## AI Control Center integration

OpenClaw in WSL2 section:
- **Start**: `nohup openclaw gateway --port 18789 > /tmp/openclaw-gateway.log 2>&1 & disown`
- **Stop**: kills port 18789
- **💬 Chat**: opens `openclaw chat` in new terminal
