# ./docker/php/Dockerfile
FROM php:8.1-fpm

# PHP設定
COPY php.ini /usr/local/etc/php/

# 必要パッケージとPHP拡張
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
  default-mysql-client zlib1g-dev libzip-dev unzip curl ca-certificates \
  && docker-php-ext-install pdo_mysql zip \
  && rm -rf /var/lib/apt/lists/*

# Composer（/usr/local/bin/composer に配置）
RUN curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer

# ← ここから app ユーザーの作成
# ホストからUID/GIDを受け取り（未指定なら1000/1000）
ARG UID=1000
ARG GID=1000

# グループ/ユーザー作成（存在チェック付き）
RUN set -eux; \
  if ! getent group "${GID}" >/dev/null; then groupadd -g "${GID}" app; fi; \
  if ! id -u app >/dev/null 2>&1; then useradd -m -u "${UID}" -g "${GID}" -s /bin/bash app; fi; \
  # XDG関連ディレクトリ整備（Composer等が使う）
  mkdir -p /home/app/.config /home/app/.cache /home/app/.local/share; \
  chown -R "${UID}:${GID}" /home/app; \
  # 作業ディレクトリも用意＆権限付与（後でボリュームが被さっても安全）
  mkdir -p /var/www && chown -R "${UID}:${GID}" /var/www

# 環境変数と作業ディレクトリ
ENV HOME=/home/app \
  XDG_CONFIG_HOME=/home/app/.config \
  XDG_CACHE_HOME=/home/app/.cache \
  XDG_DATA_HOME=/home/app/.local/share

WORKDIR /var/www

USER app

