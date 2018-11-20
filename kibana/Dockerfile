ARG KIBANA_VERSION=6.3.1

FROM proemergotech/kibana-plugin-builder:base-$KIBANA_VERSION

ARG KIBANA_VERSION=6.3.1

RUN apk add --no-cache --virtual .build-deps curl && \
  # Install kibana
  curl -sLO https://github.com/elastic/kibana/archive/v$KIBANA_VERSION.tar.gz && \
  tar -xzf v$KIBANA_VERSION.tar.gz && \
  rm -f v$KIBANA_VERSION.tar.gz && \
  mv kibana-$KIBANA_VERSION /kibana && \
  cd /kibana && \
  yarn kbn bootstrap && \
  # Remove build deps
  apk del .build-deps && \
  rm -rf /tmp/*

WORKDIR /kibana
