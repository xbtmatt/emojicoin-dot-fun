FROM econialabs/aptos-cli:4.1.0 AS aptos-cli

FROM ubuntu:noble

COPY --from=aptos-cli /usr/local/bin/aptos /usr/local/bin/aptos

ARG EMOJICOIN_MODULE_ADDRESS=${EMOJICOIN_MODULE_ADDRESS} \
    PUBLISHER_PRIVATE_KEY=${PUBLISHER_PRIVATE_KEY}

ENV EMOJICOIN_MODULE_ADDRESS=${EMOJICOIN_MODULE_ADDRESS} \
    PUBLISHER_PRIVATE_KEY=${PUBLISHER_PRIVATE_KEY}

RUN test -n "${EMOJICOIN_MODULE_ADDRESS}" \
    && test -n "${PUBLISHER_PRIVATE_KEY}"

WORKDIR /app

RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        ca-certificates \
        git=1:2.43* \
        curl=8.5* \
        jq=1.7* \
    && rm -rf /var/lib/apt/lists/*

ARG emojicoin_dir=src/move/emojicoin_dot_fun
ARG rewards_dir=src/move/rewards
COPY ${emojicoin_dir} move/emojicoin_dot_fun
COPY ${rewards_dir} move/rewards
RUN rm -rf move/emojicoin_dot_fun/build move/rewards/build

ARG host_base=src/docker/test-deployer
COPY ${host_base}/sh sh
COPY ${host_base}/json json

RUN chmod +x sh/build-publish-payloads.sh \
    && chmod +x sh/build-batch-fund-payloads.sh \
    && sh/build-batch-fund-payloads.sh \
    && sh/build-publish-payloads.sh

ENTRYPOINT [ "bash", "sh/entrypoint.sh" ]
