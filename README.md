# FlowCanvas Plugins

FlowCanvas 官方维护的 Codex 插件仓库。连接用户本机运行的 FlowCanvas，管理画布、节点、连线与素材，并调用已配置的模型生成图片、视频、音频和文本。

插件免费；生成内容是否收费，取决于你在 FlowCanvas 中使用的模型服务。插件通过本机 MCP 连接，不提供云端画布服务。

## 安装前准备

- 安装并运行 FlowCanvas，版本需支持「连接外部 Agent」及独立 MCP 端口。
- 在左下角「连接外部 Agent」中开启「本地连接」。
- 在 FlowCanvas 设置中配置所需模型。
- 安装 Codex 桌面端及 CLI，确保终端可运行 `codex`。
- 安装 Node.js 22 LTS 或更高版本，确保 `node` 和 `npx` 在 Codex 的 PATH 中可用。首次启动需要访问 npm 下载桥接程序。

## 安装插件

在终端运行：

```bash
codex plugin marketplace add juemingai/flowcanvas-plugins --ref main
codex plugin add flowcanvas@flowcanvas --json
```

重新加载插件，必要时重启 Codex，在新聊天中选择 FlowCanvas。

先发送：

> 使用 FlowCanvas 插件的 MCP 工具列出画布和可用图片模型，暂时不要生成。

确认工具能返回画布和模型后，可以继续：

> 在我选定的画布中，用我选择的模型生成一张橙色纸船漂浮在蓝色湖面的极简插画。

插件会先确认画布与模型，再提交生成、查询同一节点，并展示结果。FlowCanvas 中打开的同一画布会同步更新。

## 更新和卸载

```bash
# 拉取最新市场与插件信息
codex plugin marketplace upgrade flowcanvas

# 查看实际安装版本
codex plugin list --marketplace flowcanvas --json

# 卸载插件（不会删除 FlowCanvas 画布和素材）
codex plugin remove flowcanvas@flowcanvas
```

更新后重新加载插件；如果仍显示旧版本，可卸载后重新安装，再查看实际版本确认。

## 排障

- **提示无法连接**：确认 FlowCanvas 已启动、本地连接已开启。桥接会读取运行时文件发现实际地址，端口占用顺延时不必手改配置。
- **技能已加载但没有 MCP 工具**：检查 Codex 的 MCP 启动错误，重新加载插件或重启 Codex。仅看到技能说明不等于连接成功。
- **出现旧地址 `28765/mcp` 的迁移错误**：检查是否曾手动配置同名 `flowcanvas` MCP。该地址已废弃；确认后移除旧手工连接，使用本插件的自动发现连接。不要同时维护两份同名连接。
- **没有可用模型**：在 FlowCanvas 设置中配置模型，凭据保存在 FlowCanvas 中，无需粘贴给聊天。
- **首次启动失败**：检查 `node --version`、`npx --version`、npm 网络访问，以及 Codex 进程能否找到 npx。
- **生成耗时较长**：继续查询原节点，不重复提交生成，避免额外消耗额度。

从此前的本机开发版迁移时，可在确认后运行 `codex plugin remove flowcanvas@flowcanvas-local`，然后安装本仓库插件，避免两个版本同时启用。

## 数据与运行方式

插件固定通过 `npx -y @flowcanvas/cli@1.7.1 mcp` 连接本机 FlowCanvas，工具和参数由 FlowCanvas 后端提供。插件不新增中转服务器；实际生成请求由 FlowCanvas 按所选模型配置发送给对应服务。画布和产物保存在用户配置的本机工作区。

本插件面向本机 Codex 使用，不支持直接从 ChatGPT 网页或手机访问电脑上的 FlowCanvas。

## 反馈与维护

在 [GitHub Issues](https://github.com/juemingai/flowcanvas-plugins/issues) 反馈问题。请提供操作系统、Codex／FlowCanvas 版本、插件版本及脱敏后的 MCP 错误，不要提交 API Key、运行时文件、完整配置或私人画布。

维护和发布步骤见 [维护说明](MAINTAINING.md)。本仓库通过 GitHub 插件市场分发，尚未在 OpenAI 官方公开目录上架。
