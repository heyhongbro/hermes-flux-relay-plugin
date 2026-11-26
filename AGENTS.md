# AGENTS.md - hermes-flux-relay-plugin 工程纪律与安全红线

本仓库为 **完全开源** 仓库，任何提交都将对全球开发者公开审计。接手开发的 Agent 必须无条件遵守以下红线：

## 一、零信任与宿主机安全红线（不可违背）

1. **Loopback-Only 硬约束**：`sidecar/` 代理目标地址必须严格限制为 `127.0.0.1` 或 `[::1]`，启动时校验 `/api/status` 确认为真实 Hermes Gateway；严禁开放任意 Host/IP 转发或充当内网跳板；
2. **禁止持久化网关明文密码**：严禁插件在磁盘明文落盘用户的 Hermes 网关登录密码；默认在 Noise `IK` E2EE 隧道内按客户端隔离透传凭据（免密场景仅经 `0600` UDS 签发绑定 `clientPubKey` 的短效作用域 Token）；
3. **移除 `/auth/*` 泛白名单**：严格校验并限制可透传路径白名单，阻断一切 `../` 路径穿越；
4. **物理双隧道隔离**：`client_admin`（用户设备管理全权）与 `peer_roomlink`（跨网关群聊 RoomLink）必须物理分离；`peer_roomlink` 隧道严禁放行单聊、Shell、文件或管理配置 RPC；
5. **1-RTT 防重放与 `prologue` 绑定**：严禁在 Noise `IK` msg 1（0-RTT）携带任何业务 RPC；`prologue` 必须焊入 `protocol_version || instanceId || clientPubKey || gwPubKey || tunnel_kind`；
6. **下行控制指令防伪**：执行任何 `CMD_*` 下行指令前，必须校验 Control Plane Ed25519 签名、单调递增 `cmdEpoch` 防重放，且目标 `targetPopId` 必须存在于**离线根公钥验签通过的 `RegionCatalog`** 中，拒绝跳转到任意未签名地址；
7. **无回环命令行明文泄露**：Pre-Auth Key 严禁依赖裸 `--auth-key <secret>` 驻留 shell history / `ps aux`，必须支持 stdin 管道或读取后立即删除的 `0600` 临时文件。

## 二、开闭源边界铁律

1. **严禁污染开源仓**：绝对禁止在本仓库出现任何闭源云平台（`flux-cloud-platform`）的调度算法细节、内部域名拓扑、运营私钥片段、风控阈值或个人凭据；
2. **协议单一真值源**：所有公共协议变更必须先更新 `protocol/` Schema 与 `protocol/test-vectors/` 跨语言黄金测试向量，再推进 `sidecar/` 与 `plugin-py/` 实现。
