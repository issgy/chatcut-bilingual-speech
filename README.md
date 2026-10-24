# chatcut-bilingual-speech

在 ChatCut 中把一段英文演讲视频剪成**两条中英双语短视频**的 AI 助手技能（skill）。流程覆盖主题提炼、A-roll 剪辑、双语字幕、Google Drive 随机 B-roll、飞书交付和本地清理，并在六个关键节点停下来等你确认。

## 它会做什么

| 节点 | 内容 | 需要你做的 |
| --- | --- | --- |
| 1. 源视频 | 核对你提供的视频链接或本地文件 | 提供具体视频（不会自动搜索替代） |
| 2. 主题地图 | 提炼两个独立主题，给出源时间段、英文原文、中文翻译、拼接顺序和风险 | 确认主题地图 |
| 3. A-roll | 在 ChatCut 建可编辑时间轴，按地图剪辑，保留原声、不加 BGM（默认 1920×1080 / 30fps） | 在 ChatCut 里审片确认 |
| 4. 双语字幕 | 英上中下、白色粗体黑描边、每种语言最多一行，超长按语义拆分 | 确认字幕样式与时间轴 |
| 5. B-roll 切入点 | 询问每条视频首次进入 B-roll 的时间点，然后从素材库按分类随机抽取、静音叠化 | 给出每条的切入点 |
| 6. 最终成片 | 核验画面、字幕与音频，确认后自动导出、上传飞书、核验并清理临时文件 | 确认最终时间轴 |

节点之外的步骤自动推进；权限、登录或工具故障会如实报告，不会绕过或虚报成功。

## 使用前提

本技能只包含流程规则，不附带任何账号、凭证或软件。完整流程需要你的 AI 助手环境里具备：

- **ChatCut**：创建/编辑时间轴、导入媒体、字幕、预览与导出
- **Google Drive**：列举素材库子文件夹并下载选中的视频
- **飞书云盘**：向指定文件夹上传并回读核验
- 一个可读写的任务工作目录（启用清理时还需要可恢复的"移入废纸篓"能力）

这些能力可以来自连接器、插件或已认证的 CLI。缺少某项时，对应步骤会暂停并说明缺什么；主题分析等独立步骤不受影响。

## 安装

把整个文件夹放进你的 AI 助手的个人技能目录，保持 `SKILL.md`、`references/`、`config.example.json` 的相对位置不变。

以 Codex 为例：

```bash
git clone https://github.com/issgy/chatcut-bilingual-speech.git ~/.codex/skills/chatcut-bilingual-speech
```

其他支持 skill 的助手同理，放到它们各自的技能目录即可，也可以直接让助手帮你安装。

## 配置

首次使用时，助手会在"源视频"节点一并询问素材库和交付位置，你可以直接在对话里提供，不必手动编辑文件。

如果想固定下来，把 `config.example.json` 复制到**任务工作区**并命名为 `chatcut-bilingual-speech.config.json`，填入你自己的地址：

| 字段 | 说明 |
| --- | --- |
| `broll_library.folder_url_or_id` | Google Drive 素材根目录的链接或 ID，子文件夹名即素材分类（如"城市夜景""雨夜城市街道"） |
| `delivery.folder_url_or_token` | 飞书交付文件夹的链接或 token |
| `subtitle_style` | 字幕起始样式（字体、1080p 下的字号、描边宽度、最大宽度比例），字体留空则自动选可用粗体 |
| `cleanup.move_task_temporary_files_to_trash` | 交付全部核验成功后，是否把本次临时媒体移入废纸篓（默认 `true`，从不永久删除） |

配置优先级：本次对话指令 > 你指定的配置文件 > 工作区 `chatcut-bilingual-speech.config.json` > 随包默认值。不要把口令、令牌、Cookie 写进配置文件。

## 使用

安装后对助手说类似：

> 用 chatcut-bilingual-speech 处理这个视频：<视频链接或本地路径>

默认产出两条，你也可以指定数量、主题或时长。

## 目录结构

```
chatcut-bilingual-speech/
├── SKILL.md                    # 主流程与六个确认节点
├── config.example.json         # 示例配置（不含真实地址）
└── references/
    ├── setup.md                # 配置与接入说明
    ├── topic-map.md            # 主题地图规则与 topic-map.json 格式
    └── broll-delivery.md       # B-roll 选材、导出、飞书交付与清理规则
```

## 说明

- 源视频必须由你提供，并由你确认拥有合法的处理依据；本技能不核验版权。
- 不会修改或删除 Google Drive 原始素材、ChatCut 云端项目或你原有的源文件。
- 你的真实配置和任务状态文件（`chatcut-bilingual-speech.config.json`、`workflow-state.json`）已在 `.gitignore` 中排除，分享时不会被一起提交。
