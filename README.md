# webshare-switch

在 SSH 终端粘贴一行 Webshare 代理资料，就能更换 Xray 的 SOCKS5 出口。脚本先试连新代理并查询出口 IP，通过后再备份配置并重启 Xray。Xray 重启失败时会尝试自动恢复旧配置。

## 安装与使用

适用于使用 systemd 的 Linux。需要 Python 3.9 或更新版本、curl 和已经配置好的 Xray。脚本修改 Xray 服务加载的单个 JSON 配置文件。

在仓库目录运行以下命令。第二条命令先测试当前代理。

```sh
sudo install -m 0755 ./webshare-switch /usr/local/bin/webshare-switch
sudo webshare-switch --check
sudo webshare-switch
```

看到输入提示后，粘贴 `地址:端口:用户名:密码` 并按回车。输入会隐藏。IPv6 地址请用方括号包住。Xray 重启时，现有代理连接会短暂断开。

默认使用 `/usr/local/etc/xray/config.json`、`/usr/local/bin/xray`、`xray.service` 和名为 `webshare` 的 SOCKS 出站。通过 `--config`、`--xray`、`--service`、`--tag` 可以指定自己的安装路径、服务名和出站标签。查询出口会经 SOCKS5 请求 `https://api.ipify.org/`。

脚本读取当前 Xray 配置中的 `outbounds`。以下片段展示所需的 SOCKS 出站结构，代理资料请替换为实际值。

```json
{
  "tag": "webshare",
  "protocol": "socks",
  "settings": {
    "address": "proxy.example.com",
    "port": 7550,
    "user": "username",
    "pass": "password"
  }
}
```

配置文件需要对 Xray 服务账户可读。脚本保留原文件所有者、组和权限，备份文件仅 root 可读写。切换前请确认 Xray 配置的读取权限适合存放代理密码。

如果这个工具帮你省了几次打开编辑器的功夫，欢迎点个 Star。

## English

Switch the Webshare SOCKS5 outbound in an existing systemd-managed Xray installation. The command tests a candidate proxy, validates the new JSON, saves a backup, and restarts Xray. It attempts to restore the previous configuration if the service fails to restart.

Install with `sudo install -m 0755 ./webshare-switch /usr/local/bin/webshare-switch`. Run `sudo webshare-switch` in your SSH client and paste `USER:PASS@HOST:PORT` at the prompt (input is shown as you type). Use `sudo webshare-switch --check` to check the saved proxy or `webshare-switch --help` for custom paths and tags. Restarting Xray briefly interrupts active connections.

Released under the MIT License.
