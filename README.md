# Claude CLI + Ollama Dev Container

A containerized development environment integrating **Claude CLI** and **Ollama**, with support for the [Vercel AI Gateway](https://ai-gateway.vercel.sh) to use multiple LLM providers and models.

Using this setup, you can:
- Run Claude CLI in a Docker container with your local code.
- Route Claude's API calls through the Vercel AI Gateway to access a wide range of models (OpenAI, Mistral, Groq, DeepSeek, etc.) without changing your code.
- Use Ollama to manage and run local or cloud models alongside Claude.

## Prerequisites

- [Docker](https://www.docker.com/)
- [Visual Studio Code](https://code.visualstudio.com/) with the [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension

## Getting Started

1. Clone the repository and open it in VS Code.
2. When prompted, select **Reopen in Container** (or run `Dev Containers: Reopen in Container` from the command palette).

VS Code will build the image from the local `Dockerfile` and start the container automatically.

## Configuration

### Base image — `BASE_IMAGE`

The Dockerfile accepts a `BASE_IMAGE` build argument so you can choose the base OS without editing the file. Technically, any Debian-based image is supported. Below are some example configurations:

| Value | Description |
|---|---|
| `debian:trixie-slim` | Minimal Debian (default) |
| `python:3.14-slim` | Debian slim with Python 3.14 pre-installed |
| `golang:1.26-trixie` | Debian trixie with Go 1.26 pre-installed |
| `node:lts-trixie-slim` | Debian slim with Node.js LTS pre-installed |

To switch, edit the `BASE_IMAGE` value in `.devcontainer/devcontainer.json`:

```jsonc
"build": {
  "dockerfile": "Dockerfile",
  "args": {
    "BASE_IMAGE": "python:3.14-slim"
  }
}
```

Then rebuild the container (`Dev Containers: Rebuild Container`).

### Environment variables — `.env.dist`

Copy `.devcontainer/.env.dist` to `.devcontainer/.env` and fill in the values:

```dotenv
ANTHROPIC_AUTH_TOKEN="vck_..."      # Claude authentication token
ANTHROPIC_API_KEY=""                # Alternative API key
# ANTHROPIC_BASE_URL="https://ai-gateway.vercel.sh"  # Vercel AI Gateway endpoint
```

Setting `ANTHROPIC_BASE_URL` to the Vercel AI Gateway allows you to route requests to any supported provider (OpenAI, Mistral, Groq, DeepSeek, etc.) without changing your code. If you want to use Vercel AI Gateway, make sure to set `ANTHROPIC_AUTH_TOKEN` with a valid token/key that has access to the gateway.

Keep ANTRHOPIC_API_KEY empty if you're using `ANTHROPIC_AUTH_TOKEN` for authentication, as they are mutually exclusive.

### Model overrides — `.claude/settings.local.json`

Claude CLI uses three internal model tiers (Haiku, Sonnet, Opus). Via `.claude/settings.local.json` you can remap each tier to any model exposed by the gateway:

```json
{
  "env": {
    "API_TIMEOUT_MS": "3000000",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek/deepseek-v3.1",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "moonshotai/Kimi-K2.5",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "moonshotai/Kimi-K2.5"
  }
}
```

| Variable | Description |
|---|---|
| `API_TIMEOUT_MS` | Maximum API call timeout in milliseconds. `3000000` = 50 minutes, useful for slow requests or high-latency models. |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Model used when Claude selects the *fast/cheap* tier (Haiku). |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Model used when Claude selects the *balanced* tier (Sonnet). |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Model used when Claude selects the *powerful* tier (Opus). |

## Usage

### Use Claude directly

```bash
claude
```

In this case Claude CLI will use the default Anthropic models. If you want to use different models, configure the overrides as described above.

### Start Ollama

To use local or cloud models with Ollama, start the server first:

```bash
ollama serve
```

### Use Claude with Ollama

With the Ollama server running, launch Claude through it:

```bash
ollama launch claude
```

Ollama will prompt you to select a model. 

You can also specify the model directly in the command:

```bash
ollama launch claude --model glm-5:cloud
```
