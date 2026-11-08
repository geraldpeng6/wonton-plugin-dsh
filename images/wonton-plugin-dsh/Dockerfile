# Agent 层：底座镜像（wonton-agent 入口）+ 官方 dsh CLI（@deepseek-ai/dsh，MIT，可再分发，打进镜像）。
FROM ghcr.io/geraldpeng6/wonton-agent-base:0.1.0@sha256:9d6e0ca87c14eee21b5b6503fb52dd863182e7a4f784dd644a4e8cb3f58989d7

# 构建期经代理拉 npm（宿主构建时用 --build-arg 传入）。
ARG HTTPS_PROXY
ARG HTTP_PROXY

# 官方 npm registry 的 tgz，按 npm dist.integrity 校验通过才安装；依赖由 npm 安装时核对。
ARG DSH_VERSION=0.2.2-rc.1
ARG DSH_SHA512=8a347d504558906d7943f43bf49cefccb07340e41f73add52ebf67e9f969c073e1af920a9f6531b619d2a2da3f3fd97ba8677ba4fbc8d516ba4d6ecb1f7a0aa6

# 底座以 USER 65532 收尾，装包要先切 root。
USER root
RUN set -eux; \
    curl --fail --silent --show-error --location \
      "https://registry.npmjs.org/@deepseek-ai/dsh/-/dsh-${DSH_VERSION}.tgz" \
      --output /tmp/dsh.tgz; \
    echo "${DSH_SHA512}  /tmp/dsh.tgz" | sha512sum --check --strict -; \
    npm install --prefix /opt/dsh --ignore-scripts --no-audit --no-fund "/tmp/dsh.tgz"; \
    rm -f /tmp/dsh.tgz; \
    /opt/dsh/node_modules/.bin/dsh --version
USER 65532:65532

# dsh 的凭据、会话和 acp profile 补丁都放在数据卷 HOME 下的 .dsh/；遥测全关。
ENV DSH_HOME=/data/agent/home/.dsh \
    DSH_TELEMETRY_MODE=DISABLED

COPY agent.toml /etc/wonton-agent/
COPY --chmod=0755 gateway /usr/local/lib/wonton-agent/gateway
COPY --chmod=0755 dsh-login /usr/local/lib/wonton-agent/dsh-login
COPY --chmod=0755 dsh-login-status /usr/local/lib/wonton-agent/dsh-login-status
COPY prompt.md /usr/local/share/wonton-agent/prompt.md
# skill 目录 COPY 整棵树：COPY --chmod 会连自动创建的目录一起改（0644 会丢掉目录的 x 位）。
COPY wonton-help/ /usr/local/share/wonton-agent/skills/wonton-help/
COPY LICENSE.dsh /usr/share/doc/wonton-plugin-dsh/LICENSE.dsh
