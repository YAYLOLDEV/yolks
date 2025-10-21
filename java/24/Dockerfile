# Use the official Eclipse Temurin image with Java 24 JRE.
# The --platform flag ensures the correct architecture is pulled for the build environment.
FROM --platform=$TARGETOS/$TARGETARCH eclipse-temurin:24-jre

LABEL author="Michael Parker" maintainer="parker@pterodactyl.io"
LABEL org.opencontainers.image.source="https://github.com/pterodactyl/yolks"
LABEL org.opencontainers.image.licenses=MIT

# Set the GeckoDriver version. You can update this as new versions are released.
ARG GECKODRIVER_VERSION=0.34.0

# Set non-interactive frontend for package installation to prevent prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages, Firefox, and GeckoDriver.
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
    # Install Firefox Extended Support Release
    firefox-esr \
    && \
    # --- Install GeckoDriver ---
    # Detect architecture to download the correct binary
    ARCH=$(dpkg --print-architecture) && \
    case "${ARCH}" in \
        amd64) GECKO_ARCH="linux64" ;; \
        arm64) GECKO_ARCH="linux-aarch64" ;; \
        *) echo "Unsupported architecture: ${ARCH}"; exit 1 ;; \
    esac && \
    # Download, extract, and install GeckoDriver
    wget -q "https://github.com/mozilla/geckodriver/releases/download/v${GECKODRIVER_VERSION}/geckodriver-v${GECKODRIVER_VERSION}-${GECKO_ARCH}.tar.gz" -O /tmp/geckodriver.tar.gz && \
    tar -xzf /tmp/geckodriver.tar.gz -C /usr/local/bin/ && \
    chmod +x /usr/local/bin/geckodriver && \
    # Clean up
    rm /tmp/geckodriver.tar.gz && \
    rm -rf /var/lib/apt/lists/*

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
