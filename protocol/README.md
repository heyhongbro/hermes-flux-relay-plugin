# Public Relay Protocol (`protocol/`)

本目录是 **Hermes Flux 中继生态的唯一公共协议真值源**，供开源客户端（`hermes-flux`）、开源网关插件（`hermes-flux-relay-plugin`）与闭源云平台（`flux-cloud-platform`）共同单向引用。

## 规划目录结构

```text
protocol/
├── proto/                      # Protobuf / JSON Schema 契约定义
│   ├── tunnel_frame.proto      # 外层路由信封、Yamux/QUIC 流复用、V2 SIGNAL_ICE 预留帧
│   ├── control_commands.proto  # 4 类签名下行指令 (CMD_MIGRATE_POP / FORCE_RECONNECT / UPDATE_STANDBY / DISCONNECT_REVOKED)
│   ├── region_catalog.proto    # 离线根私钥签名的 RegionCatalog 与 InstanceDirectory 模型
│   └── pairing_pkce.proto      # OAuth PKCE (SHA256(verifier || gwPubKey)) 与 Pre-Auth 契约
├── crypto/                     # Noise_IK (1-RTT, prologue 绑定) 与 WebCrypto AESGCM 降级规范
└── test-vectors/               # 跨语言黄金测试向量（Go / TypeScript / Python 共同校验字节级一致）
```

## 变更原则

- 协议版本遵循 SemVer，闭源服务端按固定 Git Tag 引用；
- 任何协议修改必须同步补充 `test-vectors/` 黄金测试向量；
- 所有破坏性变更必须至少保留 2 个大版本的向后兼容窗口。
