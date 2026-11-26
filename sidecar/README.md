# Go/Rust E2EE Tunnel Sidecar (`sidecar/`)

本目录承载跨平台静态编译的高性能隧道守护进程，随 `plugin-py/` 托管或通过独立 CLI（`hermes-flux-relay up`）运行，对 Hermes Gateway 内核零侵入。

## 模块划分

- `identity/`：宿主机 Ed25519 / X25519 密钥生成与 `0600` 存储、`revoked_devices.json` 离线吊销列表；
- `crypto/`：`Noise_IK` 1-RTT 握手实现与 `prologue` 强绑定、逻辑 `TunnelSession` 会话密钥管理（预留 `transport_epoch + seq` 支持 V2 无断流切换）；
- `scheduler/`：Bootstrap 双引导域、离线根公钥验签 `RegionCatalog`、并发 Region Probe 测速、响应 `CMD_MIGRATE_POP`（30s Make-Before-Break）、`CMD_FORCE_RECONNECT`、`CMD_UPDATE_STANDBY`、`CMD_DISCONNECT_REVOKED`；
- `mux/`：Yamux/HTTP2 多路复用帧层（与底层 WSS/QUIC 载波解耦，为 V2 桌面端 P2P QUIC 预留挂载点）；
- `proxy/`：物理拆分 `client_admin` 与 `peer_roomlink` 双隧道处理器；强锁 Loopback-Only（`127.0.0.1`）、路径白名单与按 `clientDeviceId` 隔离的隧道内 CookieJar。
