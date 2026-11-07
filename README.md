# dsh

开发流程见[插件开发指南](https://github.com/geraldpeng6/wonton-docs/blob/main/develop.md)。

dsh（DeepSeek Harness）是住在云吞里的 Agent：DeepSeek 官方的 dsh（`@deepseek-ai/dsh`，经它自带的
`--profile acp` 说 ACP）用核心分配的 Matrix 身份和主人聊天，经 Wonton MCP 总线调用工具。模型网关接好时
默认走网关，不用账号、也不用填 key；没接网关时 Agent 照常启动，在聊天里发 `/wonton login` 或从插件页打开
登录网页，贴一个自己的 DeepSeek API key（platform.deepseek.com 创建）。登录态存在数据目录的
`.credentials.yaml`（0600），容器重建后还在；网关接入或卸掉后重启容器即切换。

dsh 的会话日志和遥测默认回传上游，插件把它关了（`DSH_TELEMETRY_MODE=DISABLED`；接网关时同时关掉
`session-log-deepseek` 插件，会话日志不发给网关）。

## 构建镜像

```bash
docker build -t ghcr.io/geraldpeng6/wonton-plugin-dsh:0.1.0 -f images/wonton-plugin-dsh/Dockerfile images/wonton-plugin-dsh
```

构建出来的是本地 Agent 镜像，`docker image inspect -f '{{.Id}}'` 就是要写回 compose 的摘要。
桥镜像由主仓库维护，`scripts/publish.sh` 只构建本仓库 `images/` 下的 Agent 镜像。

## 图标

`icon.svg` 是按插件规范手绘的线条图标（小写 d 字形），原创，随插件包以 MIT 许可证分发。DeepSeek 和 dsh
是 DeepSeek 的商标；本插件不是 DeepSeek 的官方出品。来源、上游程序和许可证的说明见 [NOTICE](NOTICE)。
