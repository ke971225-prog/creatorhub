# 第 0 期补充：Chrome 安装 + systemd 常驻（2026-10-28 上午，家里 Linux 机实测）

## Google Chrome（用户拍板安装，终端路线）
- 官方包 google-chrome-stable_current_amd64.deb（145,617,708 字节）。该机系统 DNS 解析 dl.google.com 失败，经 DoH 查址后用 curl --resolve 直连同一官方地址下载，证书主机名未变，未换镜像源、未改系统 DNS
- apt 安装成功：google-chrome-stable 156.0.8078.73-1；`google-chrome --version` = Google Chrome 156.0.8078.73；路径 /usr/bin/google-chrome
- 已启动验证：窗口正常打开，未登录任何 Google 账号（选“保持未登录状态”）
- 自此小红书可走系统 Chrome CDP 首选路线，不再回退 Patchright

## CreatorHub systemd --user 常驻
- 服务文件 ~/.config/systemd/user/creatorhub.service（WorkingDirectory=/home/kcj/creatorhub-geo，ExecStart=.venv/bin/python creatorhub.py --no-open，Restart=on-failure）
- 验证：is-enabled=enabled、is-active=active、curl 127.0.0.1:8000/health={"status":"ok"}
- 已执行 loginctl enable-linger kcj（Linger=yes）：用户未登录桌面时服务也会运行，重启自启成立
- 至此第 0 期遗留两件（Chrome/常驻）全部关闭；仅剩千问探针（待用户亲手登录千问）
