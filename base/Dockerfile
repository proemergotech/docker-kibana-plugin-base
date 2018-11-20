FROM alpine

ARG NODE_VERSION=8.11.3
ARG YARN_VERSION=1.9.4

RUN apk add --no-cache libstdc++ && \
  apk add --no-cache --virtual .build-deps curl make gcc g++ python linux-headers binutils-gold gnupg && \
  # Install nodejs
  for server in ipv4.pool.sks-keyservers.net keyserver.pgp.com ha.pool.sks-keyservers.net; do \
    gpg --keyserver $server --recv-keys \
      94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
      FD3A5288F042B6850C66B31F09FE44734EB7990E \
      71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
      DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
      C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
      B9AE9905FFD7803F25714661B63B535A4C206CA9 \
      56730D5401028683275BD23C23EFEFE93C4CFFFE \
      77984A986EBC2AA786BC0F66B01FBB92821C587A && break; \
  done && \
  curl -sfSLO https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}.tar.xz && \
  curl -sfSL https://nodejs.org/dist/v${NODE_VERSION}/SHASUMS256.txt.asc | gpg --batch --decrypt | \
    grep " node-v${NODE_VERSION}.tar.xz\$" | sha256sum -c | grep ': OK$' && \
  tar -xf node-v${NODE_VERSION}.tar.xz && \
  cd node-v${NODE_VERSION} && \
  ./configure --prefix=/usr && \
  make -j$(getconf _NPROCESSORS_ONLN) && \
  make install && \
  rm -rf /node-v${NODE_VERSION}* && \
  cd / && \
  # Install yarn
  for server in ipv4.pool.sks-keyservers.net keyserver.pgp.com ha.pool.sks-keyservers.net; do \
    gpg --keyserver $server --recv-keys \
      6A010C5166006599AA17F08146C2130DFD2497F5 && break; \
  done && \
  curl -sfSLO https://github.com/yarnpkg/yarn/releases/download/v${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz && \
  curl -sfSLO https://github.com/yarnpkg/yarn/releases/download/v${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz.asc && \
  gpg --batch --verify yarn-v${YARN_VERSION}.tar.gz.asc yarn-v${YARN_VERSION}.tar.gz && \
  mkdir /usr/local/share/yarn && \
  tar -xf yarn-v${YARN_VERSION}.tar.gz -C /usr/local/share/yarn --strip 1 && \
  ln -s /usr/local/share/yarn/bin/yarn /usr/local/bin/ && \
  ln -s /usr/local/share/yarn/bin/yarnpkg /usr/local/bin/ && \
  rm yarn-v${YARN_VERSION}.tar.gz* && \
  # Remove build deps
  apk del .build-deps && \
  rm -rf /tmp/*
