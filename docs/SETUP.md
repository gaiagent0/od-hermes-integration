# Setup Guide – vivo2 (Snapdragon X Elite)

## System overview

- **Machine**: vivo2, Windows 11 ARM64, Snapdragon X Elite
- **WSL2**: Ubuntu, user `istvan`
- **Node**: v24 via nvm (`~/.nvm/versions/node/v24.15.0/bin/`)
- **Hermes**: `~/.local/bin/hermes` (v0.14.0)
- **OpenClaw**: `~/.nvm/versions/node/v24.15.0/bin/openclaw` (v2026.5.12)

## Open Design installation

```bash
git clone https://github.com/nexu-io/open-design.git ~/open-design
cd ~/open-design

export NVM_DIR="$HOME/.nvm" && . "$NVM_DIR/nvm.sh"
nvm use 24

pnpm install
pnpm --filter @open-design/daemon build

# Critical: Hermes must be in PATH for daemon detection
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```

## Start Open Design

```bash
cd ~/open-design
export PATH="$HOME/.local/bin:$PATH"
OPENAI_BASE_URL="http://localhost:4000/v1" OPENAI_API_KEY=dummy pnpm tools-dev
```

Daemon and web ports are **dynamic** – read from log:

```bash
LOG=~/open-design/.tmp/tools-dev/default/logs
grep -oP 'http://127\.0\.0\.1:\K\d+' $LOG/daemon/latest.log | tail -1
grep -oP 'http://127\.0\.0\.1:\K\d+' $LOG/web/latest.log | tail -1
```

## WSL2 → Windows port access

Open Design binds to `127.0.0.1` (WSL-only). Use `netsh portproxy` (Admin PowerShell):

```powershell
$wslIp = (wsl hostname -I).Trim().Split(' ')[0]
$port = 33329  # replace with actual web port
netsh interface portproxy add v4tov4 listenport=$port listenaddress=127.0.0.1 connectport=$port connectaddress=$wslIp
```

The AI Control Center does this automatically when starting Open Design.

## BYOK configuration

In the Open Design web UI:

1. Settings → Execution mode → **BYOK**
2. Quick fill provider: **Custom provider**
3. Base URL: `http://127.0.0.1:4000/v1`
4. API key: `dummy`
5. Model: `groq-smart` or `lmstudio-smart`
6. **Fetch models** → should show 42+ aliases
7. Save

## Local CLI mode (recommended)

When Hermes is in PATH, Open Design detects it automatically:
- Top right: **Local CLI · Hermes · default**
- Model picker shows all OpenRouter models from Hermes config
- No BYOK/SSRF restrictions

## LiteLLM proxy

```bash
# Check
curl -s http://localhost:4000/v1/models | python3 -m json.tool | grep '"id"'

# Restart
pkill -f litellm && sleep 2
cd ~/litellm
litellm --config config.yaml --host 0.0.0.0 --port 4000 &
```
