# Docker 部署说明

本项目已经打包为公共容器镜像：

```text
ghcr.io/wolf575/mass-cc-checker:latest
```

## 环境要求

- 已安装 Docker 或 Docker Compose。
- 默认使用宿主机端口 `8080`，可以通过环境变量 `CC_PORT` 修改。

## 方式一：docker run

```bash
docker run -d \
  --name mass-cc-checker \
  -p 8080:80 \
  --restart unless-stopped \
  ghcr.io/wolf575/mass-cc-checker:latest
```

修改宿主机端口：

```bash
docker run -d \
  --name mass-cc-checker \
  -p 9000:80 \
  --restart unless-stopped \
  ghcr.io/wolf575/mass-cc-checker:latest
```

启动后访问：<http://localhost:8080>

## 方式二：docker compose

先准备 `docker-compose.yml`：

```yaml
services:
  mass-cc-checker:
    image: ghcr.io/wolf575/mass-cc-checker:latest
    container_name: mass-cc-checker
    ports:
      - "${CC_PORT:-8080}:80"
    restart: unless-stopped
```

启动：

```bash
docker compose up -d
```

停止并删除容器：

```bash
docker compose down
```

## 从源码构建

在项目根目录执行：

```bash
docker build -t ghcr.io/wolf575/mass-cc-checker:latest .
```

也可以使用仓库内的 `docker-compose.yml`（带 `build` 配置）：

```bash
docker compose up -d --build
```

## 验证服务

```bash
curl -s http://localhost:8080/ -o /dev/null -w "%{http_code}\n"

curl -s -X POST http://localhost:8080/api.php \
  -d "data=4111111111111111|12|2026|123"
```

正常情况下首页返回 `200`，`api.php` 返回 JSON，其中 `status` 为 `die`、`network` 为 `Visa`。

## 查看日志

```bash
docker logs -f mass-cc-checker
```
