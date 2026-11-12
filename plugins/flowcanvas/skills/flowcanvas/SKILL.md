---
name: flowcanvas
description: 当用户要求通过 FlowCanvas 管理本机画布、节点、连线、素材，或生成图片、视频、音频和文本时使用。需要本机运行 FlowCanvas 并开启本地连接。
---

# FlowCanvas 画布创作

使用本插件提供的 FlowCanvas MCP 工具执行操作。以运行时工具描述和 inputSchema 为准，不凭记忆编写参数，也不改用 REST 或安装另一套 CLI 技能。

## 连接

先调用 `list_canvases`。无法连接时检查：FlowCanvas 是否启动，左下角「连接外部 Agent」中的「本地连接」是否开启。提醒用户开启后重试；不要静默改变开关、固定端口或请求 API Key。桥接会读取 `~/.flowcanvas/runtime.json`，自动发现实际 MCP 地址。首次启动需要 Node.js、npx 和 npm 网络访问。

插件安装后若工具尚未出现，明确说明需要重新加载插件或在新聊天选择 FlowCanvas，不得声称已验证连接。

## 选择画布与模型

1. 用户未指定画布时，展示 `list_canvases` 返回的名称、更新时间和节点数，让用户选择或提供新画布名称。禁止默认使用最近画布或自动新建。
2. 用户指定名称时先匹配列表；唯一匹配可直接使用，多个匹配需选择，未匹配时展示候选。新建画布必须使用用户给定的名称。
3. 操作已有画布前调用 `get_canvas`，使用真实 canvas_id、node_id。
4. 生成前调用 `list_configs`，指定所需 model_type，保留 include_schema=true。使用返回的 config_id、model_key 和 parameter_schema；不虚构模型、比例、分辨率、时长或音色。
5. 用户未指定模型且存在多个可用模型时，让用户选择。无可用模型时引导在 FlowCanvas 设置中配置，凭据由 FlowCanvas 管理。

## 生成与继续创作

- 按任务使用 `generate_image`、`generate_video`、`generate_audio`、`generate_text`；所有生成都传入已确定的 canvas_id。
- 默认单次生成一个产物，数量和模型专属参数按用户要求及真实 schema 设置。付费批量任务先明确模型与数量。
- 使用已有产物作为参考时传真实 reference_node_ids，复用工具的自动连线能力；本机参考文件使用工具支持的路径参数。
- 视频模式、首尾帧、多参考及配音参数以工具说明与模型能力为准；TTS 音色通过 `list_voices` 查询。
- 生成工具异步返回 node_id / node_ids 和任务状态，立即记录。调用 `wait_for_nodes` 查询这些节点；每次 timeout_seconds 使用 45，仍在生成就继续查询并简短告知进度。
- 超时或连接中断不代表生成失败。重连后先用 `get_node` 或 `wait_for_nodes` 检查已记录节点，不重复提交生成。
- 区分成功、失败、缺失与仍在生成；失败时展示实际错误，未经用户要求不自动重试付费生成。`done` 仅表示等待结束，不能据此认定全部成功。

## 交付

完成后检查返回的 results / output 和 local_path。图片通过绝对路径 Markdown 图片展示，音频同样使用媒体展示；视频使用宿主支持的预览，无法预览时给出本地文件链接。多个结果逐一交付。文本直接展示生成内容。

同时说明画布名称和节点状态。用户打开同一画布时，改动通常会在数秒内同步。只有确认节点成功且结果存在后才能说生成完成，不能仅返回“请到桌面端查看”。

删除画布等不可恢复操作遵守对应工具的确认要求；不为验证连接调用生成或删除工具。
