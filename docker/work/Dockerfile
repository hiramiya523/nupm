# node:hydrogen-slim
FROM node@sha256:3139aa3e8915e7c135623498d29f20a75ee3bfc41cf321ceaa59470b2fffc1a5 AS builder
FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

ARG UID
ARG GID
ARG HOST_DOCKER_GROUP_GID
ARG NAME=vscode

ENV TZ Asia/Tokyo
ENV DOCKER_BUILDKIT=1
ENV NODE_ENV development

# 2.5.8
# COPY --from=composer@sha256:d989ed8c8080a36e295f5f769f685d0ec0eb9ca2b55d3e3094d98da9ce7c11ed /usr/bin/composer /usr/bin/composer
COPY --from=builder /usr/local/bin/node /usr/local/bin/
COPY --from=builder /usr/local/lib/node_modules/ /usr/local/lib/node_modules/

RUN ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm \
  && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npx \
  && npm install -g bun

RUN apt-get update -y \
  && apt-get install -y --no-install-recommends software-properties-common \
  # php
  # && add-apt-repository -y ppa:ondrej/php \
  && apt-get update -y \
  && apt-get install -y --no-install-recommends \
  # PHP
  # php8.2 php8.2-intl php8.2-mbstring php8.2-mysql php8.2-gd php8.2-fpm php8.2-xml php8.2-curl \
  vim git \
  iputils-ping net-tools dnsutils \
  && apt-get clean && rm -rf /var/lib/apt/lists/* && apt-get autoremove

COPY ./docker-debian.sh /docker-debian.sh
RUN bash /docker-debian.sh

RUN groupmod -g ${GID} $NAME && usermod -u $UID $NAME && \
  groupmod -g $HOST_DOCKER_GROUP_GID docker

RUN echo 'export LANG=C.utf8' >> /home/vscode/.bashrc \
  && echo "alias dc='docker compose'" >> /home/vscode/.bashrc

WORKDIR /workspace
ENTRYPOINT ["/usr/local/share/docker-init.sh"]
CMD ["sleep", "infinity"]
