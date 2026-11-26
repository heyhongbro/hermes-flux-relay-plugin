# hermes-flux-relay-plugin

> **定位**：Hermes Gateway 官方开源中继接入插件、E2EE 隧道守护进程与跨端公共协议契约仓库（Open-Source / Zero-Trust Auditable）。

## 仓库目录结构

```text
hermes-flux-relay-plugin/
├── AGENTS.md           # 本仓工程安全红线与开发纪律
├── protocol/           # 公共协议契约（Protobuf/JSON Schema、Noise IK、CMD_* 帧、黄金测试向量）
├── plugin-py/          # 薄 Hermes Python 插件壳与 CLI (hermes relay login/up/status/revoke)
└── sidecar/            # 开源 Go/Rust 异步隧道守护进程（Noise IK、Yamux/QUIC、Loopback-Only 守卫）
```

## 核心特性

1. **Zero-Trust True E2EE（可审计端到端加密）**：端侧私钥永不出机，基于 `Noise_IK`（1-RTT 防重放 + `prologue` 强绑定）透传业务流，中继节点仅见密文；
2. **Loopback-Only 物理安全边界**：守护进程强锁 `127.0.0.1` / `[::1]` 回环端口并执行路径白名单，严格物理拆分 `client_admin` 与 `peer_roomlink` 双隧道；
3. **Google 式防钓鱼 PKCE 配对**：支持 `hermes relay login` 生成 Universal Link + ASCII 二维码，通过 `code_challenge = SHA256(verifier || gwPubKey)` 锁死目标主机公钥；
4. **服务端动态调度服从**：通过两级寻址接收 Control Plane 签名 `CMD_MIGRATE_POP` / `CMD_FORCE_RECONNECT` / `CMD_UPDATE_STANDBY` / `CMD_DISCONNECT_REVOKED` 指令，验签后执行 30s Make-Before-Break 平滑迁流。

## 公共协议与开发规范

- 跨端公共协议契约、Noise `IK` 握手、下行 `CMD_*` 控制帧与黄金测试向量见 [`protocol/README.md`](./protocol/README.md)；
- 宿主机零信任安全与工程开发红线见 [`AGENTS.md`](./AGENTS.md)。
