# Marvis Foresight Room 部署配置

目标服务器：124.221.200.162。公网端口：8084。内部端口：18084。部署时已确认 8084/18084 未被其他服务占用。

- 应用目录：`/root/depoly/marvis_foresight_room/`
- 应用服务：`marvis_foresight_room.service`，单 worker，保持任务调度和 SSE 订阅处于同一进程。
- Nginx：使用现有 `shared_nginx` 容器，安装独立配置到 `/root/nginx/conf.d/marvis_foresight_room.conf`。
- Python：3.10 或更新版本，项目独立 `.venv`。
- 配置：服务器上的 `.env`，权限设为 600；按 `env.example` 填入 DeepSeek API key。凭据不加入 Git。
- 数据：应用自动创建 `data/marvis.db`。发布不上传仓库里的历史数据库，不覆盖服务器数据库或 `.env`。
- 健康接口：`/api/health`，检查应用及数据库连接。

安装前检查 Docker 网络和挂载、现有 Nginx 配置、systemd 服务以及 8084/18084 端口。先启动应用并验证内部健康接口，之后执行 `docker exec shared_nginx nginx -t`，通过后才 reload。验证公网主页、静态资源、健康接口及 AI 调用，并抽查已有站点。此目录的文件是部署配置，不能据此判断网站已经上线。

## 维护

```bash
systemctl status marvis_foresight_room.service
systemctl restart marvis_foresight_room.service
journalctl -u marvis_foresight_room.service -n 50 --no-pager
curl -f http://127.0.0.1:18084/api/health
curl -f http://124.221.200.162:8084/api/health
```

修改 `.env` 后需重启应用。应用已设置开机自启。公网功能与其他站点验证记录以实际发布结果为准。
