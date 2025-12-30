# SensiExtract - Burp Suite 敏感信息提取插件

**SensiExtract** 是一款专为 Burp Suite 开发的被动扫描插件，旨在自动从 HTTP 请求和响应中提取敏感信息、指纹信息以及其他感兴趣的数据。它支持通过 YAML 配置自定义正则规则，并在 Burp 的 HTTP History 中实时高亮显示匹配结果，同时提供一个独立的 Dashboard 面板用于数据的聚合展示和管理。

## ✨ 主要功能

*   **被动扫描与实时监控**：在您浏览网站或进行其他测试时，自动在后台分析流量，不干扰正常操作。
*   **高度可配置的规则系统**：
    *   使用 YAML 格式定义规则（`Rule.yaml`）。
    *   支持正则表达式（Java Regex & RE2J）。
    *   支持规则分组（敏感信息、指纹、漏洞检测等）。
    *   支持自定义高亮颜色。
*   **HTTP History 增强集成**：
    *   **实时高亮**：根据匹配规则的颜色自动高亮 Proxy History 中的请求行。
    *   **MarkInfo 展示**：在 History 列表的 "Notes" 列中直接显示匹配到的规则名称及数量（如 `Linkfinder(5), Email(2)`）。
    *   **自定义列**：优化了 History 列表，包含 Host、Method URL、Extension、Notes 等实用字段。
*   **Databoard 数据面板**：
    *   提供独立且美观的 UI 界面。
    *   按域名（Host）和规则组（Category）聚合展示数据。
    *   支持搜索和过滤提取到的信息。
    *   内置请求/响应查看器（支持大响应优化）。
*   **强大的过滤机制**：
    *   支持按 Host、文件后缀、状态码、Content-Type 进行过滤。
    *   支持只监控特定的 Burp 工具（Proxy, Repeater, Spider 等）。
*   **高性能与持久化**：
    *   使用 SQLite 数据库存储所有提取的数据，重启 Burp 不丢失。
    *   针对大响应（Large Response）进行了专门优化，主线程快速高亮，后台异步处理，避免从界面卡顿。
    *   支持超过 100KB 的大响应自动降级处理（灰色高亮），保证性能。

## 🛠️ 安装与使用

1.  **下载/构建**：下载最新的 `SensiExtract.jar` 或使用 Gradle 自行构建。
    ```bash
    ./gradlew clean build -x test
    ```
2.  **加载插件**：
    *   打开 Burp Suite -> **Extensions** -> **Add**。
    *   选择 **Java** 类型，并加载 `SensiExtract.jar`。
3.  **配置**：
    *   插件加载后会在用户目录下生成 `.burp_unexpected_info` 文件夹。
    *   `Rule.yaml`：核心规则配置文件，您可以编辑此文件添加自定义正则。
    *   `filter_config.yaml`：过滤配置文件。
    *   `unexpected_info.db`：数据存储文件。
4.  **查看结果**：
    *   **HTTP History**：关注高亮的请求以及 "Notes" 列的信息。
    *   **Databoard 标签页**：点击 Burp 一级标签 "Databoard" 查看详细统计和提取内容。

## ⚙️ 规则配置 (Rule.yaml)

规则文件位于 `~/.burp_unexpected_info/Rule.yaml`，默认包含以下分组：
*   **敏感信息**：Email, 身份证, 电话号码, 密码, AccessKey, JDBC 连接等。
*   **指纹信息**：Shiro, JWT, Swagger UI, Druid 等。
*   **基本信息**：内网 IP 等。
*   **其它**：Linkfinder (URL 提取), Source Map 等。
*   **疑似漏洞**：URL as Value, Upload Form, Debug Parameters 等。

**规则示例：**

```yaml
rules:
  - group: "敏感信息"
    rule:
      - name: "邮箱"
        loaded: true
        f_regex: "(([a-z0-9][_|\\.])*[a-z0-9]+@([a-z0-9][-|_|\\.])*[a-z0-9]+\\.((?!js|css|jpg|jpeg|png|ico)[a-z]{2,}))"
        color: "yellow"
        scope: "response body"
```

## 📝 过滤设置

在 **Databoard -> Config** 中可以进行详细设置：
*   **Scope**：仅处理 In-Scope 的请求。
*   **后缀过滤**：排除 `.css`, `.jpg`, `.png` 等静态资源。
*   **状态码**：例如排除 404 或 500。
*   **工具过滤**：只监听 Proxy 或 Spider 的流量。


---
**Author**: kk1230
**Version**: v1.0
