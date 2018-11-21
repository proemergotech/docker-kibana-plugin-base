# docker-kibana-plugin-base

Base docker image for easier kibana plugin development.

## Usage

### Production

#### Dockerfile

```
ARG KIBANA_VERSION

FROM proemergotech/kibana-plugin-builder:$KIBANA_VERSION as builder

# this must match kibana.version in package.json
ARG KIBANA_VERSION
# this must match the root directory name of the plugin
ARG PLUGIN_NAME=<YOUR PROJECT NAME>
# this must match version in package.json
ARG PLUGIN_VERSION=<YOUR PROJECT VERSION>

RUN mkdir -p /kibana-extra/$PLUGIN_NAME
WORKDIR /kibana-extra/$PLUGIN_NAME
ADD . .

RUN yarn kbn bootstrap \
    && yarn build --build-destination=bin --kibana-version=$KIBANA_VERSION \
    && mv ./bin/$PLUGIN_NAME-$PLUGIN_VERSION.zip /tmp/plugin.zip

FROM docker.elastic.co/kibana/kibana:$KIBANA_VERSION

COPY --from=builder /tmp/plugin.zip /tmp/plugin.zip

RUN kibana-plugin install file:///tmp/plugin.zip
```

### Development

#### Dockerfile

```
ARG KIBANA_VERSION

FROM proemergotech/kibana-plugin-builder:$KIBANA_VERSION

# this must match the root directory name of the plugin
ARG PLUGIN_NAME=<YOUR PROJECT NAME>

EXPOSE 5601

RUN mkdir -p /kibana-extra/$PLUGIN_NAME
WORKDIR /kibana-extra/$PLUGIN_NAME

CMD yarn kbn bootstrap && yarn start
```

#### docker-compose.yml

```$xslt
version: '3'

services:
  kibana:
    build:
      context: .
      dockerfile: Dockerfile-dev
      args:
        - KIBANA_VERSION=6.3.1
    ports:
      - "5601:5601"
    volumes:
      - .:/kibana-extra/<YOUR PORJECT NAME>
      - ./kibana.dev.yml:/kibana/config/kibana.dev.yml
```

#### kibana.dev.yml

```

---
# Default Kibana configuration from kibana-docker.
# from: https://github.com/elastic/kibana-docker/blob/master/build/kibana/config/kibana-full.yml

server.name: kibana
server.host: "0"
elasticsearch.url: http://localhost:9200
xpack.monitoring.ui.container.elasticsearch.enabled: false
```
