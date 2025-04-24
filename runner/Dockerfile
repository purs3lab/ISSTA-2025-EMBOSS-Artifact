FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y wget gnupg software-properties-common lsb-release apt-transport-https ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Add Kitware's APT repository (replace 'focal' with your Ubuntu codename if needed)
RUN wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc | apt-key add - && \
    apt-add-repository 'deb https://apt.kitware.com/ubuntu/ focal main'

# Install necessary dependencies
RUN apt-get update && \
    apt-get install -y \
    build-essential \
    cmake \
    python3 \
    curl \
    tar \
    git \
    unzip \
    libidn11 \
    jq \
    sudo \
    bash && \
    rm -rf /var/lib/apt/lists/*

# Copy the entire codeql-home directory
COPY ./codeql-home /opt/codeql-home
WORKDIR /opt/codeql-home
RUN curl -LO https://github.com/github/codeql-cli-binaries/releases/download/v2.13.1/codeql-linux64.zip && \
    unzip codeql-linux64.zip && \
    rm codeql-linux64.zip

# Create a non-root user for GitHub Actions runner
RUN useradd -m runner && \
    echo "runner ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
    mkdir -p /home/runner/actions-runner && \
    mkdir -p /home/runner/.local/bin && \
    chown -R runner:runner /home/runner

USER runner
WORKDIR /home/runner/actions-runner

# Set environment PATH
ENV PATH="/opt/codeql-home/codeql:/home/runner/.local/bin:${PATH}"

# Download and unpack GitHub Actions runner
RUN curl -o actions-runner-linux-x64-2.323.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.323.0/actions-runner-linux-x64-2.323.0.tar.gz && \
    echo "0dbc9bf5a58620fc52cb6cc0448abcca964a8d74b5f39773b7afcad9ab691e19  actions-runner-linux-x64-2.323.0.tar.gz" | shasum -a 256 -c && \
    tar xzf ./actions-runner-linux-x64-2.323.0.tar.gz

# Default command to open a shell (so user can configure runner)
CMD ["/bin/bash"]

