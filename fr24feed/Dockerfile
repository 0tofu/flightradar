FROM debian:stable-slim as builder

RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  ca-certificates \
  wget \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /fr24feed
RUN set -eux \
  && wget https://repo-feed.flightradar24.com/linux_x86_64_binaries/fr24feed_1.0.25-3_amd64.tgz \
  && tar xzvf fr24feed_1.0.25-3_amd64.tgz --strip-components 1

FROM debian:stable-slim
RUN set -eux \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
  ca-certificates \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
COPY --from=builder /fr24feed/fr24feed /usr/local/bin/fr24feed
RUN set -eux \
  && touch /etc/fr24feed.ini

EXPOSE 8754 10001 30003 30334

ENTRYPOINT ["/usr/local/bin/fr24feed"]
