

# Composer
FROM composer:2.9.5 AS composer-stage
USER $USER
WORKDIR /tmp/
ENV COMPOSER_ALLOW_SUPERUSER 1
COPY composer.lock composer.json .

# # Yarn
# FROM node:18-alpine AS yarn-stage
# USER $USER
# WORKDIR /tmp/
# COPY package.json pnpm-workspace.yaml pnpm-lock.yaml yarn.lock .
# RUN apk add --no-cache yarn && yarn install

# # Frank
FROM dunglas/frankenphp:1.12.1-php8.4 AS runner
ARG USER
ARG APP_ROOT
RUN apt-get update && apt-get install -y libzip-dev libicu-dev && \
    apt-get install -y --no-install-recommends \
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    libwebp-dev \
    libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* &&\
    install-php-extensions \
    bcmath \
    exif \
    gd \
    intl \
    mysqli \
    zip \
    imagick

# Build stuff here
RUN chown -R ${USER}:${USER} /config/caddy /data/caddy ${APP_ROOT}
USER ${USER}
# Copy composer binary
COPY --from=composer-stage /usr/bin/composer /usr/bin/composer
# Copy composer output
COPY --chown=${USER}:${USER} --from=composer-stage /tmp/* ${APP_ROOT}
WORKDIR ${APP_ROOT}
RUN composer install --no-scripts --no-progress

# Serve
# CMD ["bash"]
CMD ["frankenphp", "run", "--config", "/etc/caddy/Caddyfile", "--resume=false"]
