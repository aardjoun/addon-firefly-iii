ARG BUILD_FROM
# hadolint ignore=DL3006
FROM $BUILD_FROM

# Default ENV
ENV FIREFLY_VERSION=5.7.14

# Setup base
# hadolint ignore=DL3008,DL4006
RUN \
    set -x \
    && apt-get update && apt-get install -y --no-install-recommends \
        tzdata \
        locales \
        git \
        mariadb-client \
        nginx \
        software-properties-common \
        unzip \
    #Install and config PHP 8.0
    && curl -J -L -o /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg \
    && echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list \
    && apt-get update \
    && apt-get install  -y --no-install-recommends \
        php8.1-bcmath \
        php8.1-curl \
        php8.1-cli \
        php8.1-common \
        php8.1-fpm \
        php8.1-gd \
        php8.1-intl \
        php8.1-ldap \
        php8.1-mbstring \
        php8.1-mysql \
        php8.1-opcache \
        php8.1-readline \
        php8.1-sqlite3 \
        php8.1-xml \
        php8.1-zip \
        php8.1 \
    \
    #Install composer
    && curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /tmp/composer && chmod +x /tmp/composer \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/firefly
# hadolint ignore=SC3009,SC2039
RUN \
    curl -J -L -o /tmp/firefly.tar.gz \
        https://github.com/firefly-iii/firefly-iii/archive/${FIREFLY_VERSION}.tar.gz \
    && tar zxf /tmp/firefly.tar.gz -C \
        /var/www/firefly --strip-components=1 \
    && printf "vi_VN.UTF-8 UTF-8\nfi_FI.UTF-8 UTF-8\nsv_SE.UTF-8 UTF-8\nel_GR.UTF-8 UTF-8\nhu_HU.UTF-8 UTF-8\nro_RO.UTF-8 UTF-8\nnb_NO.UTF-8 UTF-8\nde_DE.UTF-8 UTF-8\ncs_CZ.UTF-8 UTF-8\nen_US.UTF-8 UTF-8\nes_ES.UTF-8 UTF-8\nfr_FR.UTF-8 UTF-8\nid_ID.UTF-8 UTF-8\nit_IT.UTF-8 UTF-8\nnl_NL.UTF-8 UTF-8\npl_PL.UTF-8 UTF-8\npt_PT.UTF-8 UTF-8\nru_RU.UTF-8 UTF-8\ntr_TR.UTF-8 UTF-8\nzh_TW.UTF-8 UTF-8\nzh_CN.UTF-8 UTF-8\n\n" > /etc/locale.gen \
    && locale-gen \
    && /tmp/composer install --prefer-dist --no-dev --no-scripts --no-suggest \
    \
    && rm -f /tmp/composer \
    && find /var/www/firefly -type f -name "*.md" -depth -exec rm -f {} \; \
    && apt-get purge -y --auto-remove \
        git \
        software-properties-common \
        unzip \
    && apt-get clean \
    && rm -f -r \
        /tmp/* \
        /etc/nginx \
        /var/{cache,log}/* \
        /usr/src/* \
        /var/www/firefly/Docker* \
        /var/www/firefly/docker* \
        /var/www/firefly/phpunit* \
        \
    && mkdir -p /var/log/nginx \
    && touch /var/log/nginx/error.log

# S6-Overlay
WORKDIR /

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG \
    BUILD_ARCH \
    BUILD_DATE \
    BUILD_DESCRIPTION \
    BUILD_NAME \
    BUILD_REF \
    BUILD_REPOSITORY \
    BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Paulo Costa <coostax@duck.com>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.authors="Paulo Costa <coostax@duck.com>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.vendor="Home Assistant Add-ons" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

ENTRYPOINT ["/init"]
