ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:6.2.5

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Confiure locale
ENV \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8 \
    S6_VERBOSITY="1"

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Copy Python requirements file
COPY requirements.txt /tmp/requirements.txt

# Copy in extensions list
COPY code-server.extensions /etc/code-server.extensions

# Setup base system
ARG BUILD_ARCH=amd64
ARG CODE_SERVER_VERSION="v4.11.0"
ARG HA_CLI_VERSION="4.23.0"
ARG RCLONE_VERSION="v1.62.2"
ARG CTOP_VERSION="v0.7.7"
ARG CADDY_VERSION="v2.6.4"
ARG TAILSCALE_VERSION="v1.38.4"
ARG CLOUFLARED_VERSION="2023.4.1"
ARG YQ_VERSION="v4.33.3"
# renovate: datasource=repology depName=debian_11/build-essential-mipsen versioning=loose
ARG build_essential_version="12.9"
# renovate: datasource=repology depName=debian_11/locales versioning=loose
ARG locales_version="2.31-13+deb11u5"
# no-renovate: datasource=repology depName=debian_11/uuid-runtime versioning=loose
ARG uuid_runtime_version="2.36.1-8+deb11u1"
# renovate: datasource=repology depName=debian_11/gnupg versioning=loose
ARG gnupg_version="2.2.27-2+deb11u2"
# renovate: datasource=repology depName=debian_11/openssh versioning=loose
ARG openssh_server_version="1:8.4p1-5+deb11u1"
# renovate: datasource=repology depName=debian_11/openssh versioning=loose
ARG openssh_client_version="1:8.4p1-5+deb11u1"
# no-renovate: datasource=repology depName=debian_11/docker-ce-cli versioning=loose
ARG docker_ce_cli_version="5:23.0.3-1~debian.11~bullseye"

# hadolint ignore=SC2181, DL3008
RUN \
    apt-get update \
    \
    && apt-get install -y --no-install-recommends \
        ack=3.4.0-1 \
        libarchive-tools=3.4.3-2+deb11u1 \
        build-essential="${build_essential_version}" \
        colordiff=1.0.18-1.1 \
        git=1:2.30.2-1+deb11u2 \
        iputils-ping=3:20210202-1 \
        locales="${locales_version}" \
        mariadb-client=1:10.5.18-0+deb11u1 \
        mosquitto-clients=2.0.11-1 \
        net-tools=1.60+git20181103.0eebece-1 \
        nmap=7.91+dfsg1+really7.80+dfsg1-2 \
        openssh-client="${openssh_client_version}" \
        openssl=1.1.1n-0+deb11u4 \
        python3-dev=3.9.2-3 \
        python3=3.9.2-3 \
        unzip=6.0-26+deb11u1 \
        uuid-runtime="${uuid_runtime_version}" \
        wget=1.21-1+deb11u1 \
        zip=3.0-12 \
        zsh=5.8-6+deb11u1 \
        less=551-2 \
        nano=5.4-2+deb11u2 \
        apt-transport-https=2.2.4 \
        ca-certificates=20210119 \
        gnupg="${gnupg_version}" \
        unzip=6.0-26+deb11u1 \
        cron=3.0pl1-137 \
        lsb-release=11.1.0 \
        openssh-server="${openssh_server_version}" \
        git-crypt=0.6.0-1 \
        php=2:7.4+76 \
        php-curl=2:7.4+76 \
        netcat=1.10-46 \
        htop=3.0.5-7 \
    \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen \
    \
    && curl https://bootstrap.pypa.io/get-pip.py | python3 \
    \
    && if [[ "${BUILD_ARCH}" = "aarch64" ]]; then ARCH="arm64"; fi \
    && if [[ "${BUILD_ARCH}" = "amd64" ]]; then ARCH="amd64"; fi \
    && echo "Installing Code-Server version ${CODE_SERVER_VERSION#v}" \
    && curl -J -L -s -o /tmp/code.tar.gz \
        "https://github.com/coder/code-server/releases/download/${CODE_SERVER_VERSION}/code-server-${CODE_SERVER_VERSION#v}-linux-${ARCH}.tar.gz" \
    && mkdir -p /usr/local/lib/code-server \
    && tar zxvf \
        /tmp/code.tar.gz \
        --strip 1 -C /usr/local/lib/code-server \
    \
    && ln -s /usr/local/lib/code-server/bin/code-server /usr/local/bin/code-server \
    \
    && mkdir -p /root/.code-server/extensions \
    && uuid=$(uuidgen) \
    && while read -r ext; do \
        extention="${ext%%#*}" \
        vendor="${extention%%.*}"; \
        slug="${extention#*.}"; \
        version="${ext##*#}"; \
        \
        echo "Installing Code Server extension: ${slug} by ${vendor} @ ${version} "; \
        \
        echo "https://marketplace.visualstudio.com/_apis/public/gallery/publishers/${vendor}/vsextensions/${slug}/${version}/vspackage"; \
        curl -JL --retry 5 -o "/tmp/${extention}-${version}.vsix" \
            -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36" \
            -H "x-market-user-id: ${uuid}" \
            "https://marketplace.visualstudio.com/_apis/public/gallery/publishers/${vendor}/vsextensions/${slug}/${version}/vspackage"; \
        mkdir -p "/usr/local/lib/code-server/lib/vscode/extensions/${extention}-${version}"; \
        bsdtar --strip-components=1 -xf "/tmp/${extention}-${version}.vsix" \
                    -C "/usr/local/lib/code-server/lib/vscode/extensions/${extention}-${version}" extension; \
        [ $? -ne 0 ] && exit 1; \
        sleep 1; \
    done < /etc/code-server.extensions \
    && ls -la /usr/local/lib/code-server/lib/vscode/extensions/ \
    \
    && curl -L -s -o /usr/bin/ha \
        "https://github.com/home-assistant/cli/releases/download/${HA_CLI_VERSION}/ha_${BUILD_ARCH}" \
    && chmod a+x /usr/bin/ha \
    \
    && git clone --branch master --single-branch --depth 1 \
        "https://github.com/robbyrussell/oh-my-zsh.git" ~/.oh-my-zsh \
    \
    && git clone --branch master --single-branch --depth 1 \
        "https://github.com/zsh-users/zsh-autosuggestions" \
        ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions \
    && git clone --branch master --single-branch --depth 1 \
        "https://github.com/zsh-users/zsh-syntax-highlighting.git" \
        ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting \
    \
    && sed -i -e "s#bin/bash#bin/zsh#" /etc/passwd \
    \
    && update-alternatives \
        --install /usr/bin/python python /usr/bin/python3 10 \
    \
    && pip3 install --no-cache-dir -r /tmp/requirements.txt \
    \
    && curl -J -L -s -o /usr/local/bin/ctop "https://github.com/bcicen/ctop/releases/download/v${CTOP_VERSION#v}/ctop-${CTOP_VERSION#v}-linux-${ARCH}"  \
    &&  chmod +x /usr/local/bin/ctop \
    \
    && curl -J -L -s -o /tmp/rclone.deb "https://github.com/rclone/rclone/releases/download/${RCLONE_VERSION}/rclone-${RCLONE_VERSION}-linux-${ARCH}.deb" \
    && dpkg -i /tmp/rclone.deb &> /dev/null \
    \
    && curl -L -s  \
        "https://github.com/caddyserver/caddy/releases/download/${CADDY_VERSION}/caddy_${CADDY_VERSION#v}_linux_${ARCH}.tar.gz" \
        | tar zxvf - -C /tmp/ \
    && mv /tmp/caddy /usr/local/bin/ \
    \
    && curl -L -s \
        "https://pkgs.tailscale.com/stable/tailscale_${TAILSCALE_VERSION#v}_${ARCH}.tgz" \
        | tar zxvf - -C /opt/ --strip-components 1 \
    && ln -s /opt/tailscale /usr/local/bin/ \
    && ln -s /opt/tailscaled /usr/local/bin/ \
    \
    && curl -J -L -s -o /usr/bin/cloudflared "https://github.com/cloudflare/cloudflared/releases/download/${CLOUFLARED_VERSION}/cloudflared-linux-${ARCH}" \
    && chmod +x /usr/bin/cloudflared \
    \
    && curl -J -L -s -o /usr/bin/yq "https://github.com/mikefarah/yq/releases/download/${YQ_VERSION}/yq_linux_${ARCH}" \
    && chmod +x /usr/bin/yq \
    \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg &> /dev/null \
    && echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && apt-get update &> /dev/null \
    && apt-get install -y --no-install-recommends \
    docker-ce-cli="${docker_ce_cli_version}" \
    &> /dev/null \
    && mv /usr/bin/docker /usr/local/bin/.undocked \
    \
    && apt-get purge -y --auto-remove \
        bsdtar \
        build-essential \
        python3-dev \
        uuid-runtime \
    \
    \
    && find /usr/local \
        \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    \
    && rm -fr \
        /root/.cache \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* \
    \
    && touch /var/log/lastlog \
    \ 
    && rm -fr \
        /etc/cron.d/* \
        /etc/cron.daily/* 

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Elcajon" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Add-on" \
    org.opencontainers.image.authors="Elcajon" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/elcajon-tech/repository-stable" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
