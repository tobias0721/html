# Claude Code 101 学习索引

来源：<https://www.youtube.com/playlist?list=PLmWCw1CzcFilebjK89WLb5cAvM8K0cLB3>

处理方式：
- 使用 `huashu-md-html` 的 YouTube/URL 转 markdown 流程作为入口。
- MarkItDown 对 playlist 和单视频当前只抓到 YouTube 页脚，因此补用 `yt-dlp` 获取播放列表元数据。
- 第一集检查到 YouTube 自动字幕/自动翻译轨道中包含 `zh-Hans`，但没有作者上传的正式字幕。
- 这里先做中文学习辅助，不输出整套逐字中文字幕。

## 课程结构

| 序号 | 英文标题 | 中文学习标题 | 时长 | 学习目标 |
|---:|---|---|---:|---|
| 1 | What is Claude Code? | Claude Code 是什么 | 2:56 | 建立对 Claude Code 的基本定位：它不是普通聊天窗口，而是面向代码库工作的 coding agent。 |
| 2 | Installing Claude Code | 安装 Claude Code | 3:02 | 理解安装前提、CLI 入口和首次运行时需要确认的环境条件。 |
| 3 | How Claude Code Works | Claude Code 如何工作 | 2:51 | 理解它如何读取项目、执行命令、修改文件，并通过上下文推进任务。 |
| 4 | Your first Claude Code prompt | 第一个 Claude Code prompt | 2:27 | 学会把需求写成可执行任务，而不是只写泛泛的问题。 |
| 5 | The CLAUDE.md file | CLAUDE.md 文件 | 3:01 | 理解项目级指令文件如何让 agent 记住仓库规则、命令和偏好。 |
| 6 | The Explore -> Plan -> Code -> Commit workflow in Claude Code | Explore -> Plan -> Code -> Commit 工作流 | 3:12 | 学习先探索、再计划、再编码、最后提交的基本协作节奏。 |
| 7 | Context Management in Claude Code | Claude Code 的上下文管理 | 3:52 | 理解上下文窗口、文件读取、任务聚焦和长期协作中的信息管理。 |
| 8 | MCP in Claude Code | Claude Code 里的 MCP | 3:37 | 理解 MCP 如何把外部工具、数据源和服务接入 agent。 |
| 9 | Hooks in Claude Code | Claude Code Hooks | 3:22 | 理解 hooks 如何在特定事件前后执行自动化检查或流程。 |

## 推荐学习顺序

1. 先看第 1-3 集，建立 Claude Code 的基本模型：它能看项目、跑命令、改文件。
2. 再看第 4-6 集，重点练习如何把一个模糊需求拆成可执行任务。
3. 最后看第 7-9 集，把上下文管理、MCP、hooks 看成让 agent 稳定工作的工程化机制。

## 观看辅助

YouTube 当前提供自动中文字幕轨道。观看时可以：

1. 打开视频。
2. 点 `CC` 或字幕按钮。
3. 在字幕设置里选择 `Chinese (Simplified)` / `中文（简体）`，如果没有显示就选择自动翻译到中文。

建议每集按这个模板做笔记：

```md
## 第 X 集：标题

### 这一集解决什么问题

### 关键概念

### 和我的 Codex 使用有什么关系

### 可以马上尝试的动作

### 不懂的问题
```

## 本地文件

- 播放列表元数据：`youtube-study/playlist_videos.jsonl`
- MarkItDown 原始尝试：`youtube-study/playlist_raw.md`
- 第一集 MarkItDown 原始尝试：`youtube-study/01-what-is-claude-code.raw.md`
