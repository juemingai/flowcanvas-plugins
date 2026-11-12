# 维护与发布

## 文件结构

- `.agents/plugins/marketplace.json`：公开市场 `flowcanvas`，插件名 `flowcanvas`。
- `plugins/flowcanvas/plugin.json`：插件版本、说明及图标配置。
- `plugins/flowcanvas/mcp.json`：固定版本 CLI 的 stdio 启动配置。
- `plugins/flowcanvas/skills/flowcanvas/SKILL.md`：面向 MCP 的中文使用指引。

市场中的 source.path 相对于仓库根目录解析。不得写入开发机绝对路径、API Key、模型配置、私人画布或本机验证记录。仅发布插件所需文件，不复制 FlowCanvas 主程序源码。

## 开发与检查

后续修改从最新 main 创建独立功能或修复分支。修改插件内容时提升 plugin.json 的语义化版本并更新 CHANGELOG.md；不要直接修改用户的插件缓存。

发布前确认：

1. JSON、技能 YAML、市场路径及图标引用有效；CLI 固定版本能从 npm 下载。
2. 在干净的 Codex 环境安装，不依赖开发机手工 MCP 或全局 FlowCanvas skill。
3. 测试应用未启动、本地连接关闭、工具发现、画布读取和模型查询。
4. 取得测试者对画布、模型和费用的确认后，验证一次生成、异步等待、结果回写和聊天展示。
5. 分别记录 macOS 和 Windows 的验证结果；未验证的平台不得声称已通过。

## 发布

人工验证通过后，将功能分支合并到 main 并推送。首个空仓库没有 main 时，也先在功能分支准备提交，经人工确认后再建立 main 推送。

用户默认从 main 安装；使用 marketplace upgrade 后，检查实际插件版本，必要时重新安装。正式版本可附加 Git tag，并在 GitHub Release 中放更新说明。

GitHub 仓库市场与 OpenAI 官方目录是两条不同发布路径。本仓库不包含官方目录审核包，不通过上传私有插件来替代 GitHub 分发。
