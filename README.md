# Local setup guide for OpenAI gpt-oss models with Codex CLI, Ollama, LM Studio, and MLX on Apple Silicon

## Installation

**Codex CLI**
```bash
npm i -g @openai/codex
# or on macOS
brew install codex
```

**Ollama** — install from ollama.ai, then:
```bash
ollama pull gpt-oss:120b   # ~65 GB
ollama pull gpt-oss:20b    # ~13 GB (lighter alternative)
```

---

## Running gpt-oss:120b

**Interactive Ollama session**
```bash
ollama run gpt-oss:120b
```

**With Codex CLI (quick)**
```bash
codex --oss --model gpt-oss:120b
```

**With Codex CLI (configured profile)**

Create/edit `~/.codex/config.toml`:
```toml
[model_providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"

[profiles.gpt-oss-120b-ollama]
model_provider = "ollama"
model = "gpt-oss:120b"
```

Then run:
```bash
codex --oss --profile gpt-oss-120b-ollama
```

---

## Alternative Local Providers

**LM Studio**
```toml
[model_providers.lms]
name = "LM Studio"
base_url = "http://localhost:1234/v1"

[profiles.gpt-oss-120b-lms]
model_provider = "lms"
model = "gpt-oss:120b"
```

**MLX (Apple Silicon)**
```bash
pip install mlx-lm
mlx_lm.server --model SuperagenticAI/gpt-oss-20b-8bit-mlx --port 8888
```
```toml
[model_providers.mlx]
name = "MLX LM"
base_url = "http://localhost:8888/v1"

[profiles.gpt-oss-20b-8bit-mlx]
model_provider = "mlx"
model = "SuperagenticAI/gpt-oss-20b-8bit-mlx"
```

---

## Remote Access (Tailscale/Network)

**Bind Ollama to network**
```bash
sudo mkdir -p /etc/systemd/system/ollama.service.d
sudo bash -c 'cat > /etc/systemd/system/ollama.service.d/override.conf <<EOF
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
EOF'
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

**Connect from another machine**
```bash
export OLLAMA_HOST=<ip>:11434
ollama run gpt-oss:120b

# Or with Codex
CODEX_OSS_BASE_URL=http://<ip>:11434/v1 codex --oss --model gpt-oss:120b
```

---

## Context Length

For larger projects, increase context window:

| Provider | Method |
|----------|--------|
| Ollama | `/set parameter num_ctx` in session |
| LM Studio | `lms load <model> --context-length <n>` |
| MLX | Configure via server launch params |

---

## With LLM CLI

```bash
uv tool install llm
llm install llm-ollama
llm -m gpt-oss:120b "your prompt"
```

---

## Hardware Requirements

- **gpt-oss:120b** — ~65 GB, needs substantial VRAM or RAM
- **gpt-oss:20b** — ~13 GB, more accessible
- **gpt-oss-20b-8bit-mlx** — quantized for Apple Silicon
