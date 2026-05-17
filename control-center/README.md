# AI Control Center – Open Design & OpenClaw Integration

Patches for `C:\AI\apps\control-center`.

## Services added

### Open Design (`wsl2_open_design` type)

- **Start**: `cd ~/open-design && export PATH=$HOME/.local/bin:$PATH && nohup pnpm tools-dev > /tmp/open-design.log 2>&1 & disown`
- **Stop**: `pnpm tools-dev stop`
- **Port detection**: reads from `~/open-design/.tmp/tools-dev/default/logs/web/latest.log`
- **Port forwarding**: `netsh portproxy` added automatically on start (requires Admin)
- **Buttons**: Start, Stop, 🖥 Desktop (opens web UI in Windows browser)

### OpenClaw (`wsl2_bg` type, port 18789)

- **Start**: `export NVM_DIR="$HOME/.nvm" && . "$NVM_DIR/nvm.sh" && nohup openclaw gateway --port 18789 > /tmp/openclaw-gateway.log 2>&1 & disown`
- **Stop**: `fuser -k 18789/tcp`
- **Buttons**: Start, Stop, 💬 Chat (opens `openclaw chat` in terminal)

## Backend changes (`main.py`)

1. `open_design_ports()` – reads dynamic ports from daemon/web logs
2. `_wsl_ip()` – gets WSL2 IP via `hostname -I`
3. `_portproxy_add(port)` / `_portproxy_del(port)` – netsh wrappers
4. `svc_status` handler for `wsl2_open_design` type
5. `svc_start`/`svc_stop` handlers for both types
6. `/api/launch/open-design` – opens web UI in Windows browser
7. `/api/launch/openclaw` – opens `openclaw chat` terminal

## Frontend changes (`index.html`)

1. `SVC_COLORS`: `open-design: #818CF8`, `openclaw: #F472B6`
2. `renderRow`: Desktop button for open-design, Chat button for openclaw
3. `launchOpenDesign()` / `launchOpenClaw()` functions

## Port conflict fix

`mtp-8b` moved from port **8082** → **8083** (was conflicting with `freecc`).

## Admin requirement

`netsh portproxy` requires Administrator. Run `restart.ps1` from Admin PowerShell
or add `-Verb RunAs` to the `start.ps1` script.
