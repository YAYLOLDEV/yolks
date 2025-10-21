# Use the official Eclipse Temurin image with Java 24 JRE.
# The --platform flag ensures the correct architecture is pulled for the build environment.
FROM --platform=$TARGETOS/$TARGETARCH eclipse-temurin:24-jre

LABEL author="Michael Parker" maintainer="parker@pterodactyl.io"
LABEL org.opencontainers.image.source="https://github.com/pterodactyl/yolks"
LABEL org.opencontainers.image.licenses=MIT

# Set non-interactive frontend for package installation to prevent prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages, Chromium, and its driver.
# We use Chromium because Google Chrome does not have an official ARM64 package.
# This approach works for both AMD64 and ARM64 architectures.
RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
    # General utilities
    curl \
    lsof \
    ca-certificates \
    openssl \
    git \
    tar \
    sqlite3 \
    fontconfig \
    tzdata \
    iproute2 \
    libfreetype6 \
    tini \
    zip \
    unzip \
    jq \
    wget \
    gnupg \
    # Browser and driver
    chromium \
    chromium-driver \
    # Required dependencies for browser environments
    libnss3 \
    libxss1 \
    libappindicator3-1 \
    xdg-utils \
    # Clean up apt cache to reduce image size
    && rm -rf /var/lib/apt/lists/*

# Setup user and working directory
RUN useradd -m -d /home/container -s /bin/bash container
USER container
ENV USER=container HOME=/home/container
WORKDIR /home/container

STOPSIGNAL SIGINT

COPY --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]
