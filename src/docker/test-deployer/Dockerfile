FROM econialabs/aptos-cli:4.1.0 AS aptos-cli

FROM ubuntu:noble
COPY --from=aptos-cli /usr/local/bin/aptos /usr/local/bin/aptos

ARG EMOJICOIN_MODULE_ADDRESS
ARG PUBLISHER_PRIVATE_KEY

ENV EMOJICOIN_MODULE_ADDRESS=${EMOJICOIN_MODULE_ADDRESS}
ENV PUBLISHER_PRIVATE_KEY=${PUBLISHER_PRIVATE_KEY}

RUN test -n "${EMOJICOIN_MODULE_ADDRESS}" \
    && test -n "${PUBLISHER_PRIVATE_KEY}"

WORKDIR /app

RUN apt-get update                                 \
    && apt-get install --no-install-recommends -y  \
        ca-certificates                            \
        git=1:2.43*                                \
        curl=8.5*                                  \
        jq=1.7*                                    \
    && rm -rf /var/lib/apt/lists/*

COPY src/move/emojicoin_dot_fun/sources/* move/emojicoin_dot_fun/sources
COPY src/move/emojicoin_dot_fun/Move.toml move/emojicoin_dot_fun/Move.toml

COPY src/move/rewards/sources/* move/rewards/sources
COPY src/move/rewards/Move.toml move/rewards/Move.toml

COPY src/docker/test-deployer/sh sh
COPY src/docker/test-deployer/json json

RUN chmod +x sh/build-publish-payloads.sh       \
    && chmod +x sh/build-batch-fund-payloads.sh \
    && sh/build-batch-fund-payloads.sh          \
    && sh/build-publish-payloads.sh

ENTRYPOINT [ "bash", "sh/entrypoint.sh" ]
