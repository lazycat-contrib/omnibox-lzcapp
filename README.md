# omnibox-lzcapp

[OmniBox（小黑）](https://github.com/import-ai/omnibox) 的懒猫微服 LPK 打包（镜像模式，LPK v2）。

- 包名：`community.lazycat.app.omnibox`
- 上游镜像：`ghcr.io/import-ai/omnibox-{web,backend,wizard}`（三个组件共享版本号）+ postgres/minio/weaviate/kafka/mailpit
- 构建方式：GitHub Actions 云端构建（`ca-x/lazycat-github-action@v1`），本地无需 Docker
- 版本源：`omnibox-backend` 镜像 tag（`0.1.54` 三段式 semver）
- 发布目标：喵喵商店（私有/社区商店）

## 结构（10 服务）

| 服务 | 镜像 | 作用 |
|------|------|------|
| web | omnibox-web | SPA + nginx（自带 /api 代理到 backend）|
| backend | omnibox-backend | 主后端（用户/收藏/权限/SEO）|
| wizard | omnibox-wizard | AI 能力服务（RAG/Agent）|
| wizard-worker | omnibox-wizard | AI 异步任务 worker（Kafka 消费）|
| postgres | postgres:17.5 | 主数据库 |
| minio | minio | 对象存储（附件/文件）|
| weaviate | weaviate:1.36 | 向量数据库 |
| kafka | bitnami/kafka:4.0 | 任务队列 |
| mail-server | mailpit:v1.29 | 本地邮件（注册验证码）|

## 多入口

- 主入口 `https://omnibox.<domain>`：OmniBox 主界面
- 次入口 `https://mail-omnibox.<domain>`：Mailpit 邮箱（注册验证码在这里看）

## 部署参数

- `openai_api_key` / `openai_base_url`：OpenAI 兼容 API（embeddings + 对话模型），AI 功能必需

## 免密登录

三阶段联动：request 拦截 `/api/v1/*` 登录/注册请求捕获凭据 → response 2xx 持久化 → browser 注入 `simple-inject-password` 自动填充。
