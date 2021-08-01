FROM debian:stable-slim as builder
# ビルド用依存ライブラリインストール.
RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  ca-certificates \
  git \
  gcc \
  make \
  pkg-config \
  librtlsdr-dev \
  libncurses-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
# ビルド.
WORKDIR /build
RUN set -eux \
  && git clone https://github.com/flightaware/dump1090.git . \
  && make BLADERF=no HACKRF=no LIMESDR=no
# dump1090 options.
RUN set -eux \
  && echo '{"type": "dump1090-docker"}' > /build/public_html/status.json \
  && echo '{"rings": []}' > /build/public_html/upintheair.json

# dump1090イメージ作成.
FROM debian:stable-slim
RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  libncurses6 \
  librtlsdr0 \
  nginx \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
COPY --from=builder /build/dump1090 /usr/local/bin/dump1090
COPY --from=builder /build/public_html /app
COPY nginx.conf /nginx.conf
COPY mime.types /mime.types
COPY run.sh /run.sh

EXPOSE 8080 30005

ENTRYPOINT ["/run.sh"]
