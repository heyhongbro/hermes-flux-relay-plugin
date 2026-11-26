# Hermes Python Plugin Shell (`plugin-py/`)

本目录承载面向 `hermes-agent` 的轻量 Python 插件外壳与 CLI 入口，负责生命周期托管与用户交互，**不直接跑高并发反向代理流量**（流量由 `sidecar/` 守护进程承载）。

## 核心职责

- `plugin.yaml`：Hermes `PluginManager` 清单文件；
- `__init__.py`：注册 `gateway:startup` / `gateway:shutdown` 钩子自动拉起与优雅回收 `sidecar` 子进程；
- `cli.py`：提供用户级命令：
  - `hermes relay login`（输出 HTTPS Universal Link + ASCII 二维码，执行 PKCE 锁公钥绑定与本机回环/粘贴 Code 换票）
  - `hermes relay up`（支持 `--auth-key-stdin` / `--auth-key-file`，无交互启动隧道）
  - `hermes relay status`（展示当前 Primary / Standby POP、Region RTT 与隧道在线态）
  - `hermes relay devices` / `hermes relay revoke <device>`（列出并本地+云端双端吊销设备）
- 二进制自举校验器：首次安装按平台下载 GitHub Releases 上的 `sidecar` 静态二进制，**强制验证 `SHA256SUMS` 与 Cosign 签名**，校验不通过立即阻断。
