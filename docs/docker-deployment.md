# Docker 镜像部署指南

## 概述

本项目已配置 GitHub Actions 自动构建和推送 Docker 镜像到 GitHub Container Registry (GHCR)。

## 可用镜像

- **Backend**: `ghcr.io/pingfury108/labelllm/backend:latest`
- **Frontend**: `ghcr.io/pingfury108/labelllm/frontend:latest`

## 快速部署

### 使用预构建镜像

```bash
# 使用生产环境的 docker-compose 文件
docker compose -f docker-compose.prod.yaml up -d
```

### 手动拉取镜像

```bash
# 拉取最新的镜像
docker pull ghcr.io/pingfury108/labelllm/backend:latest
docker pull ghcr.io/pingfury108/labelllm/frontend:latest
```

## 镜像标签

- `latest`: 最新的主分支构建
- `main` / `master`: 主分支构建
- `v*`: 发布版本标签
- `pr-*`: Pull Request 构建（仅用于测试）

## 镜像架构

所有镜像都支持多架构：
- `linux/amd64` (x86_64)
- `linux/arm64` (ARM64)

## 环境变量配置

### Backend 配置

创建 `.env` 文件配置后端环境变量：

```bash
# .env
# 基础设置
DEBUG=False
ENVIRONMENT=production

# MinIO 存储配置
MINIO_ACCESS_KEY_ID=MekKrisWUnFFtsEk
MINIO_ACCESS_KEY_SECRET=XK4uxD1czzYFJCRTcM70jVrchccBdy6C
MINIO_ENDPOINT=localhost:9000
MINIO_INTERNAL_ENDPOINT=minio:9000
MINIO_BUCKET=label-llm-test

# MongoDB 配置
MongoDB_DSN=mongodb://root:mypassword@mongo:27017
MongoDB_DB_NAME=label_llm

# Redis 配置
REDIS_DSN=redis://redis:6379/11

# 安全设置
SECRET_KEY="?*hsbRq5c9gpjBp~:oHU+7s8,I.67ewohfsib1=17dw@.q9r4Iidop:Oi_5oIYgw"
```

### Frontend 配置

前端镜像在构建时已包含必要的配置，如需自定义可通过环境变量或重新构建。

## 服务端口

- **Frontend**: `8086:80`
- **Backend**: `16666:8080`
- **MinIO**: `9000:9000` (API), `9001:9001` (Console)
- **MongoDB**: `16019:27017`
- **Redis**: `16280:6379`

## 访问地址

部署成功后，可通过以下地址访问：

- **Web 界面（标注者）**: http://localhost:8086/supplier
- **管理界面（运营者）**: http://localhost:8086/operator
- **MinIO 控制台**: http://localhost:9001
  - 用户名: `user`
  - 密码: `password`

## 数据持久化

所有重要数据都通过 Docker volumes 持久化：
- `redis_data`: Redis 数据
- `mongo_data`: MongoDB 数据
- `minio_data`: MinIO 文件存储

## 自动构建流程

GitHub Actions 会在以下情况触发镜像构建和推送：

1. **推送到主分支** (`main`/`master`)
2. **创建新的标签** (`v*`)
3. **Pull Request** 到主分支
4. **手动触发** (workflow_dispatch)

## 本地开发 vs 生产环境

- **本地开发**: 使用 `docker-compose.yaml` (从源码构建)
- **生产环境**: 使用 `docker-compose.prod.yaml` (使用预构建镜像)

## 故障排除

### 检查日志

```bash
# 查看所有服务日志
docker compose -f docker-compose.prod.yaml logs

# 查看特定服务日志
docker compose -f docker-compose.prod.yaml logs backend
docker compose -f docker-compose.prod.yaml logs frontend
```

### 镜像拉取失败

如果镜像拉取失败，请确保：
1. 仓库是公开的或你有访问权限
2. 网络连接正常
3. Docker 已正确配置

### 更新镜像

```bash
# 停止服务
docker compose -f docker-compose.prod.yaml down

# 拉取最新镜像
docker compose -f docker-compose.prod.yaml pull

# 重新启动
docker compose -f docker-compose.prod.yaml up -d
```

## 安全注意事项

1. **生产环境**请修改默认密码和密钥
2. **网络安全**配置防火墙规则
3. **数据备份**定期备份数据卷
4. **监控**配置日志和监控系统 