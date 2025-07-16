FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Install Docker CLI
RUN apt-get update && apt-get install -y \
    docker.io \
    curl \
    git \
    && rm -rf /var/lib/apt/lists/*

# Optional: Install Deno (can also use the devcontainer feature)
RUN curl -fsSL https://deno.land/install.sh | sh

# Optional: Install Supabase CLI
RUN npm install -g supabase
