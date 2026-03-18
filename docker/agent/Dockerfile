FROM ubuntu:22.04
ARG DEBIAN_FRONTEND=noninteractive

# Base deps (merged into single layer)
RUN apt-get update && apt-get install -y \
  ca-certificates \
  curl \
  gnupg \
  gnupg2 \
  lsb-release \
  git \
  jq \
  bash \
  unzip \
  wget \
  python3 \
  python3-pip \
  && rm -rf /var/lib/apt/lists/*

# Docker official repo (for compose plugin)
RUN mkdir -p /etc/apt/keyrings \
  && curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
  && chmod a+r /etc/apt/keyrings/docker.gpg \
  && echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" \
  > /etc/apt/sources.list.d/docker.list

RUN apt-get update && apt-get install -y \
  docker-ce-cli \
  docker-compose-plugin \
  && rm -rf /var/lib/apt/lists/*

# gh
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
  && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
  > /etc/apt/sources.list.d/github-cli.list \
  && apt-get update && apt-get install -y gh \
  && rm -rf /var/lib/apt/lists/*

# AWS CLI v2
RUN curl -fsSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o /tmp/awscliv2.zip \
  && unzip /tmp/awscliv2.zip -d /tmp \
  && /tmp/aws/install \
  && rm -rf /tmp/aws /tmp/awscliv2.zip

# Atlassian CLI (acli)
RUN mkdir -p -m 755 /etc/apt/keyrings \
  && wget -nv -O- https://acli.atlassian.com/gpg/public-key.asc \
  | gpg --dearmor -o /etc/apt/keyrings/acli-archive-keyring.gpg \
  && chmod go+r /etc/apt/keyrings/acli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/acli-archive-keyring.gpg] https://acli.atlassian.com/linux/deb stable main" \
  > /etc/apt/sources.list.d/acli.list \
  && apt-get update \
  && apt-get install -y acli \
  && rm -rf /var/lib/apt/lists/*

# Node.js 20 LTS + npm
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
  && apt-get install -y nodejs \
  && rm -rf /var/lib/apt/lists/*

# Codex CLI
RUN npm install -g @openai/codex

# Claude CLI
RUN npm install -g @anthropic-ai/claude-code
ENV IS_SANDBOX=1

# Claude settings
RUN mkdir -p /root/.claude
COPY config/claude/managed-settings.json /etc/claude-code/managed-settings.json
COPY docker/agent/entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

WORKDIR /work
ENTRYPOINT ["/entrypoint.sh"]
