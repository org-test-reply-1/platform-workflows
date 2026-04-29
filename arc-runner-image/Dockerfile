# Dockerfile — Custom ARC Runner
# Estende l'immagine base GitHub Actions Runner con:
#   - Docker CLI (per build di immagini nei workflow)
#   - .NET SDK 10.0
#   - Utility di sistema aggiuntive

FROM ghcr.io/actions/actions-runner:latest

USER root

# ------------------------------------------------------------------ #
# Dipendenze base e prerequisiti per i repository esterni
# ------------------------------------------------------------------ #
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    gnupg \
    lsb-release \
    unzip \
    jq \
    git \
  && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------------ #
# Docker CLI (solo client — il daemon gira nel sidecar dind)
# ------------------------------------------------------------------ #
RUN install -m 0755 -d /etc/apt/keyrings \
  && curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
     | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
  && chmod a+r /etc/apt/keyrings/docker.gpg \
  && echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
     https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
     > /etc/apt/sources.list.d/docker.list \
  && apt-get update \
  && apt-get install -y --no-install-recommends docker-ce-cli \
  && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------------ #
# .NET SDK 10.0
# ------------------------------------------------------------------ #
RUN curl -fsSL https://dot.net/v1/dotnet-install.sh \
    | bash /dev/stdin \
      --channel 10.0 \
      --install-dir /usr/local/dotnet \
  && ln -s /usr/local/dotnet/dotnet /usr/local/bin/dotnet

ENV PATH="$PATH:/usr/local/dotnet"
ENV DOTNET_ROOT="/usr/local/dotnet"

# ------------------------------------------------------------------ #
# Verifica installazioni
# ------------------------------------------------------------------ #
RUN docker --version \
  && dotnet --version

# Torna all'utente runner non privilegiato
USER runner