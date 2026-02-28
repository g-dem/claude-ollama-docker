# =========================
# Ollama + Claude
# =========================
# Supported values:
#   debian:trixie-slim  (default)
#   python:3.14-slim
#   golang:1.26-trixie
ARG BASE_IMAGE=debian:trixie-slim
FROM ${BASE_IMAGE}

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    curl \
    ca-certificates \
    zstd \
    && rm -rf /var/lib/apt/lists/*

# Install Ollama
RUN curl -fsSL https://ollama.com/install.sh | sh

# Install Claude
RUN curl -fsSL https://claude.ai/install.sh | bash

ENV PATH="$HOME/.local/bin:$PATH"
RUN echo 'export PATH="/root/.local/bin:$PATH"' >> /etc/bash.bashrc

CMD ["tail", "-f", "/dev/null"]
