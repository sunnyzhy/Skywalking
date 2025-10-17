# Profile

SkyWalking 的不同版本对 JDK 版本有不同的要求，主要涉及 OAP Server 和 Agent 两部分的兼容性。以下是官方明确的版本对应关系，方便你根据实际 JDK 环境选择合适的 SkyWalking 版本：

## SkyWalking 和 JDK 版本的对应关系表

|SkyWalking 版本|OAP Server 支持的 JDK 版本|Agent 支持的 JDK 版本|说明|
|--|--|--|--|
|10.x|JDK 11 ~ 21|JDK 8 ~ 21|OAP 最低要求 JDK 11，Agent 兼容 Java 8+|
|9.x|JDK 11 ~ 21|JDK 8 ~ 21|OAP 最低要求 JDK 11，Agent 兼容 Java 8+|
|8.x（最后一个 LTS 8.16.0）|JDK 8 ~ 17|JDK 6 ~ 17|最后一个支持 JDK 8 运行 OAP 的版本，Agent 兼容 Java 6+|
|7.x|JDK 8 ~ 14|JDK 6 ~ 14|较老版本，已停止维护|
|6.x 及更早|JDK 8 ~ 12|JDK 6 ~ 12|已停止维护，不推荐使用|

关键说明:

1. OAP Server 与 JDK 的兼容性
   - 9.x 及以上：OAP Server 必须运行在 JDK 11 及以上版本（官方编译目标为 JDK 11），无法在 JDK 8 环境启动（会报类版本不兼容错误）。
   - 8.x 及以下：OAP Server 可运行在 JDK 8 及以上版本，适合仍在使用 JDK 8 的环境。
2. Agent 与 JDK 的兼容性
   - Agent 对 JDK 的兼容性更广，9.x Agent 支持 JDK 8 ~ 21，8.x Agent 甚至支持 JDK 6 ~ 17。
   - Agent 可以运行在比 OAP Server 更低版本的 JDK 上（例如：OAP 用 JDK 11，Agent 用 JDK 8），但建议 Agent JDK 版本 ≤ OAP JDK 版本，避免潜在兼容性问题。

## OAP Server 和 Agent 部署

**SkyWalking Agent 不需要和 apache-skywalking-apm-bin（OAP Server + UI）安装在同一台机器上，它们是完全独立的组件，可以分开部署。**

- apache-skywalking-apm-bin：包含 OAP Server（后端服务）和 UI（前端界面），负责接收、存储、分析 Agent 发送的数据，并通过 UI 展示。
- SkyWalking Agent：是一个轻量级的探针（JAR 包），需要附加到被监控的 Java 应用进程中，负责收集调用链路、性能指标等数据，并发送给 OAP Server。

两者的关系是：Agent 向 OAP Server 发送数据，OAP Server 处理数据，UI 展示数据。这两者可以部署在不同机器、不同容器甚至不同网络环境中（只要网络互通）。
