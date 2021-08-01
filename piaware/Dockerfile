FROM debian:stable-slim as builder
# ビルド用依存ライブラリインストール.
RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  ca-certificates \
  git \
  wget \
  devscripts \
  build-essential \
  debhelper \
  tcl-dev \
  tcl8.6-dev \
  autoconf \
  python3-dev \
  python3-venv \
  dh-systemd \
  libz-dev \
  libboost-system-dev \
  libboost-program-options-dev \
  libboost-regex-dev \
  libboost-filesystem-dev \
  libssl-dev \
  chrpath \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
# piawareパッケージビルド.
WORKDIR /tmp
RUN set -eux \
  && git clone https://github.com/flightaware/piaware_builder.git \
  && cd /tmp/piaware_builder \
  && ./sensible-build.sh buster \
  && cd ./package-buster \
  && dpkg-buildpackage -b
# piaware用tcl-tlsパッケージビルド.
WORKDIR /tmp
RUN set -eux \
  && git clone https://github.com/flightaware/tcltls-rebuild.git \
  && cd /tmp/tcltls-rebuild \
  && ./prepare-build.sh buster \
  && cd ./package-buster \
  && dpkg-buildpackage -b

# piawareイメージ生成.
FROM debian:stable-slim
RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  ca-certificates \
  procps \
  libssl1.1 \
  libtcl8.6 \
  libboost-filesystem1.67.0 \
  libboost-program-options1.67.0 \
  libboost-regex1.67.0 \
  libboost-system1.67.0 \
  libexpat1 \
  libreadline7 \
  net-tools \
  iproute2 \
  tclx8.4 \
  tcl8.6 \
  tcllib \
  itcl3 \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
COPY --from=builder /tmp/piaware_builder/piaware_5.0_amd64.deb /tmp/piaware.deb
COPY --from=builder /tmp/tcltls-rebuild/tcl-tls_1.7.16-1+fa1_amd64.deb /tmp/tcl-tls.deb
RUN set -eux \
  && dpkg -i /tmp/tcl-tls.deb \
  && dpkg -i /tmp/piaware.deb \
  && rm -rf /tmp/*

ENTRYPOINT ["/usr/bin/piaware", "-debug"]
