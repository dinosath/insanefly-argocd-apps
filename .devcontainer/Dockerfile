FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Install mise
ARG MISE_VERSION=latest
RUN curl -fsSL https://mise.run | sh \
    && echo 'eval "$(~/.local/bin/mise activate bash)"' >> /home/vscode/.bashrc \
    && echo 'eval "$(~/.local/bin/mise activate zsh)"' >> /home/vscode/.zshrc

# Ensure mise is on PATH
ENV PATH="/home/vscode/.local/bin:${PATH}"

# Install system deps that mise-managed tools may need
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y --no-install-recommends \
        curl \
        ca-certificates \
        sudo \
    && rm -rf /var/lib/apt/lists/*