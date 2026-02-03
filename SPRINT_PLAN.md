# OpenClaw Full Installation - Sprint Plan

**Objetivo**: Instalación completa del ecosistema OpenClaw con todos los componentes
**Fecha**: 2026-02-02
**Ubicación base**: `~/Dev/`

---

## Estado Actual (Actualizado 2026-02-02)

| Componente | Estado | Ubicación | Versión |
|------------|--------|-----------|---------|
| OpenClaw Core | ✅ Instalado | `~/Dev/openclaw/` | 2026.2.1 |
| ClawHub CLI | ✅ Instalado | Global npm | 0.5.0 |
| Lobster | ✅ Instalado | `~/Dev/lobster/` | 2026.1.21-1 |
| Skills Bundled | ✅ 22 ready | Built-in | - |
| Skills Community | ✅ 11 instalados | `~/.openclaw/workspace/skills/` | - |
| Awesome Skills | ✅ Clonado | `~/Dev/awesome-openclaw-skills/` | Referencia |
| ffmpeg + yt-dlp | ✅ Instalado | Homebrew | - |
| Whisper | ✅ Instalado | pipx | - |
| Obsidian CLI | ✅ Instalado | npm global | - |

---

## SPRINT 1: Core Infrastructure
**Duración estimada**: 15 min
**Dependencias**: Node.js 22+, pnpm

### 1.1 ClawHub CLI (Registry de Skills)
```bash
# Clonar repositorio
git clone https://github.com/openclaw/clawhub.git ~/Dev/clawhub
cd ~/Dev/clawhub && pnpm install && pnpm build

# Crear symlink
ln -sf ~/Dev/clawhub/bin/clawhub ~/bin/clawhub
```

### 1.2 Lobster (Workflow Engine)
```bash
# Clonar repositorio
git clone https://github.com/openclaw/lobster.git ~/Dev/lobster
cd ~/Dev/lobster && pnpm install && pnpm build

# Crear symlink
ln -sf ~/Dev/lobster/bin/lobster ~/bin/lobster
```

### 1.3 Validación Sprint 1
```bash
openclaw --version    # ✓ 2026.2.1
clawhub --version     # ✓ Verificar
lobster --version     # ✓ Verificar
```

---

## SPRINT 2: Skills Foundation
**Duración estimada**: 20 min
**Dependencias**: Sprint 1

### 2.1 Crear estructura de skills
```bash
mkdir -p ~/.openclaw/skills/{bundled,community,custom}
```

### 2.2 Instalar skills bundled que faltan dependencias
Los 27 skills marcados "missing" necesitan CLIs externos:

| Skill | CLI Requerido | Instalación |
|-------|---------------|-------------|
| apple-notes | memo | `brew install memo` |
| apple-reminders | remindctl | `brew install remindctl` |
| bird | bird-cli | `npm i -g bird-cli` |
| camsnap | ffmpeg | `brew install ffmpeg` |
| clawhub | clawhub | Sprint 1.1 |
| gifgrep | gifgrep | `cargo install gifgrep` |
| gog | gog-cli | OAuth setup required |
| goplaces | goplaces | API key required |
| imsg | imsg-cli | macOS only |
| local-places | N/A | Proxy setup |
| model-usage | codexbar | `npm i -g codexbar` |
| nano-banana-pro | N/A | API key (Gemini) |
| notion | notion-cli | OAuth setup |
| obsidian | obsidian-cli | `npm i -g obsidian-cli` |
| openai-whisper | whisper | `pip install openai-whisper` |
| openhue | openhue | `brew install openhue` |
| ordercli | ordercli | Region-specific |
| peekaboo | peekaboo | macOS app |
| sag | sag | ElevenLabs API |
| sherpa-onnx-tts | sherpa-onnx | `pip install sherpa-onnx` |
| slack | slack-cli | OAuth setup |
| songsee | songsee | `pip install songsee` |
| spotify-player | spogo | `cargo install spogo` |
| summarize | yt-dlp | `brew install yt-dlp` |
| trello | trello-cli | API key required |
| voice-call | plugin | Extension |
| wacli | wacli | WhatsApp Business |

### 2.3 Instalar CLIs básicos (sin API keys)
```bash
# Homebrew packages
brew install ffmpeg yt-dlp

# Python packages
pip install openai-whisper sherpa-onnx songsee

# Rust packages
cargo install gifgrep spogo

# Node packages
npm i -g obsidian-cli codexbar
```

---

## SPRINT 3: Awesome Skills Collection
**Duración estimada**: 30 min
**Dependencias**: Sprint 1, Sprint 2

### 3.1 Clonar colección de referencia
```bash
git clone https://github.com/VoltAgent/awesome-openclaw-skills.git ~/Dev/awesome-openclaw-skills
```

### 3.2 Instalar top skills por categoría

#### Productividad (Prioridad Alta)
```bash
clawhub install todoist
clawhub install linear
clawhub install google-calendar
clawhub install notion-sync
```

#### DevOps (Prioridad Alta)
```bash
clawhub install docker-compose
clawhub install kubernetes
clawhub install tailscale
clawhub install vercel
```

#### AI/LLM (Prioridad Alta)
```bash
clawhub install openai-assistant
clawhub install perplexity
clawhub install anthropic-mcp
```

#### Browser Automation
```bash
clawhub install playwright-agent
clawhub install puppeteer-scraper
```

#### Smart Home
```bash
clawhub install homeassistant
clawhub install philips-hue
```

---

## SPRINT 4: Channel Configuration
**Duración estimada**: 15 min
**Dependencias**: Sprint 1

### 4.1 WhatsApp (Principal)
```bash
openclaw channels login whatsapp
# Escanear QR con WhatsApp móvil
```

### 4.2 Telegram (Alternativo)
```bash
# Crear bot en @BotFather
# Obtener token
openclaw config set channels.telegram.token "BOT_TOKEN"
openclaw config set channels.telegram.allowFrom '["YOUR_CHAT_ID"]'
```

### 4.3 Discord (Opcional)
```bash
# Crear app en Discord Developer Portal
openclaw config set channels.discord.token "BOT_TOKEN"
```

---

## SPRINT 5: Gateway & Service
**Duración estimada**: 10 min
**Dependencias**: Sprint 4

### 5.1 Configurar gateway auth
```bash
openclaw config set gateway.auth.enabled true
openclaw config set gateway.auth.token "$(openssl rand -hex 32)"
```

### 5.2 Instalar como servicio (macOS)
```bash
openclaw onboard --install-daemon
```

### 5.3 Iniciar gateway
```bash
openclaw gateway run --port 18789 --bind loopback
```

### 5.4 Verificar
```bash
openclaw doctor
openclaw channels status --probe
curl http://localhost:18789/health
```

---

## SPRINT 6: Advanced Setup
**Duración estimada**: 20 min
**Dependencias**: Todos los anteriores

### 6.1 Lobster Workflows
```bash
cd ~/Dev/lobster
# Crear workflow de ejemplo
lobster init my-workflow
lobster add skill github
lobster add skill notion
lobster run
```

### 6.2 MCP Servers Integration
```bash
# Agregar MCP servers al config
openclaw config set mcp.servers.sequential '{"command":"npx","args":["-y","@anthropic/mcp-sequential"]}'
openclaw config set mcp.servers.context7 '{"command":"npx","args":["-y","@anthropic/mcp-context7"]}'
```

### 6.3 Custom Skills Directory
```bash
mkdir -p ~/.openclaw/skills/custom/my-skill
# Crear skill personalizado siguiendo Agent Skill Convention
```

---

## SPRINT 7: Security Hardening
**Duración estimada**: 10 min
**Dependencias**: Sprint 5

### 7.1 Audit de seguridad
```bash
openclaw security audit --deep
```

### 7.2 Configurar allowlists
```bash
# Solo permitir mensajes de números específicos
openclaw config set channels.whatsapp.allowFrom '["+1234567890"]'

# Requerir mención en grupos
openclaw config set channels.whatsapp.groups '{"*":{"requireMention":true}}'
```

### 7.3 Permisos de archivos
```bash
chmod 700 ~/.openclaw
chmod 600 ~/.openclaw/openclaw.json
chmod 700 ~/.openclaw/credentials
```

---

## Resumen de Comandos de Actualización

```bash
# Script: ~/bin/openclaw-full-update
#!/bin/bash
set -e

echo "🦞 Updating OpenClaw ecosystem..."

# Core
cd ~/Dev/openclaw && git pull --rebase && pnpm install && pnpm build

# ClawHub
cd ~/Dev/clawhub && git pull --rebase && pnpm install && pnpm build

# Lobster
cd ~/Dev/lobster && git pull --rebase && pnpm install && pnpm build

# Awesome skills reference
cd ~/Dev/awesome-openclaw-skills && git pull --rebase

echo "✅ All components updated!"
```

---

## Checklist Final

- [ ] Sprint 1: ClawHub + Lobster instalados
- [ ] Sprint 2: Skills bundled con dependencias
- [ ] Sprint 3: Top skills de awesome collection
- [ ] Sprint 4: Al menos 1 canal configurado
- [ ] Sprint 5: Gateway corriendo como servicio
- [ ] Sprint 6: Workflows y MCP configurados
- [ ] Sprint 7: Security audit pasado

---

## Referencias

- [OpenClaw Docs](https://docs.openclaw.ai/)
- [ClawHub](https://clawhub.ai/)
- [Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills)
- [Lobster Workflows](https://github.com/openclaw/lobster)
