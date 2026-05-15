# Claude Code 101 中文学习笔记

来源：<https://www.youtube.com/playlist?list=PLmWCw1CzcFilebjK89WLb5cAvM8K0cLB3>

这份笔记不是逐字字幕，而是基于课程字幕整理的学习版：保留知识点、案例、操作路径和你后续使用 Codex/Claude Code 时真正需要记住的判断。

## 一句话总览

Claude Code 不是“更懂代码的聊天框”，而是一个能进入代码库、读取文件、运行命令、修改文件、调用外部工具，并通过验证结果来推进任务的 coding agent。

最重要的学习主线是：

1. 先理解 agentic loop：输入目标 -> 收集上下文 -> 调用工具/改代码 -> 验证结果 -> 继续迭代。
2. 再学会用 `Plan Mode` 管住复杂任务：先读代码和做方案，不急着改文件。
3. 用 `CLAUDE.md` / `AGENTS.md` 这类项目记忆文件稳定协作规则。
4. 用 context management、MCP、skills、hooks 让 agent 变成可控的工程助手。

## 1. Claude Code 是什么

Claude Code 的核心能力：

- 读取并理解整个项目目录。
- 编辑文件、创建文件、运行 shell command。
- 执行测试、构建、安装依赖，并根据命令输出继续判断。
- 必要时接入外部工具或服务，例如文档、项目管理系统、数据库、浏览器、MCP server。
- 在终端、VS Code、JetBrains、Claude Desktop、Web 等不同入口中工作。

普通 Claude Chat 和 Claude Code 的关键差别：

| 普通聊天 | Claude Code |
|---|---|
| 主要靠你复制粘贴代码 | 直接进入代码库工作 |
| 输出建议或代码片段 | 能修改文件并执行命令 |
| 不天然知道项目结构 | 可以搜索、读取、理解项目 |
| 更像顾问 | 更像受控执行者 |

你要建立的基本心智模型：

> Claude Code = LLM + 工具调用 + 项目上下文 + 权限控制 + 验证循环。

## 2. 安装和入口怎么选

课程讲了几种入口：

- Terminal：功能更新最快，最适合重度使用。
- VS Code / JetBrains：适合想在 IDE 内直接使用 Claude Code 的场景。
- Claude Desktop：适合让 Claude 在后台跑任务，自己继续做别的事。
- Claude Web：适合远程处理 GitHub repo，或并行开多个 session。

学习建议：

- 初学时优先用 Terminal，因为你能清楚看到它在读什么、跑什么命令、改了什么文件。
- 如果是写前端 UI，再结合浏览器测试工具或 Chrome extension，让它能实际验证页面。
- 如果你只是让它远程处理 GitHub repo，可以用 Web，但本地项目上下文会弱一些。

## 3. Claude Code 的工作循环

课程把 Claude Code 的工作方式解释为 agentic loop：

1. 你给出 prompt。
2. Claude Code 判断需要哪些上下文。
3. 它读取文件、搜索代码、运行工具或命令。
4. 它采取行动，例如改文件、装包、跑测试。
5. 它验证结果是否达到目标。
6. 没达到就继续循环，直到结果可验证或需要你介入。

这和普通聊天最大的区别是：它不是一次性回答，而是在“做事 -> 看结果 -> 再做事”。

你在这个循环里有三个控制点：

- 可以补充上下文：告诉它目标、约束、已有文件、参考实现。
- 可以打断：发现方向不对时及时纠正。
- 可以调整权限：决定是否让它自动改文件、是否每次命令都确认。

## 4. 第一个 prompt 怎么写

课程里的案例是给一个应用加 dark mode toggle：

需求不是简单写“帮我加暗色模式”，而是写成：

- 在 header 上增加 toggle switch。
- 支持用户在 light mode 和 dark mode 之间切换。
- 基于现有 light theme 找到合适的 contrast color。
- 先进入 plan mode，让 Claude 读代码并给出计划。

这个案例的关键不是暗色模式本身，而是 prompt 写法：

| 弱 prompt | 强 prompt |
|---|---|
| 帮我加暗色模式 | 在 header 增加 light/dark toggle，并基于现有主题选择可读性好的对比色 |
| 改一下 UI | 说明用户入口、影响范围、视觉约束、验收标准 |
| 直接写代码 | 先 plan，再 review，再执行 |

你可以直接套用的 prompt 模板：

```text
我想实现 [功能]。
请先进入分析/计划阶段，不要改文件。

目标：
- [用户能完成什么]
- [入口在哪里]
- [影响哪些页面/模块]

约束：
- 遵守当前项目风格
- 不引入不必要依赖
- 需要说明如何验证

请先探索代码结构，给我实施计划和风险点。
```

## 5. `CLAUDE.md` / 项目记忆文件

`CLAUDE.md` 的作用是给 Claude Code 提供项目级记忆。它像一个项目 onboarding script，告诉 agent：

- 技术栈是什么。
- 常用命令是什么。
- 代码风格是什么。
- 文件应该放在哪里。
- 哪些模式优先，哪些写法不要用。

课程里的例子是一个 Next.js 15 项目：

- 使用 App Router。
- 使用 Tailwind。
- 使用 Drizzle ORM。
- 命令包括 dev server、test、lint。
- 代码风格包括 two-space indentation、prefer named exports。
- API routes 放在 `app/api`。
- 能用 server actions 时优先 server actions。

这和你的 Codex 使用非常相关。你现在这个工作树里的 `AGENTS.md` 本质上就是同类文件：把长期偏好、路径、飞书规则、Obsidian 规则写给 agent。

建议写入项目记忆文件的内容：

- 项目目标：这个仓库是干什么的。
- 关键路径：源码、数据、输出、脚本分别在哪里。
- 常用命令：测试、构建、预览、发布。
- 风格规则：命名、目录、语言、注释、提交规范。
- 安全规则：哪些文件不能展示、不能提交、不能改。
- 反复纠正过的习惯：只要你多次纠正 agent，就应该沉淀进记忆文件。

不要一开始就写很长。课程建议先让 Claude 工作一段时间，看你在哪里反复纠正它，再把必要规则写进去。

## 6. Explore -> Plan -> Code -> Commit

课程最重要的工作流是：

> Explore -> Plan -> Code -> Commit

不要一上来就让 Claude 写代码。先让它探索和计划，能减少后续返工。

课程里的案例是给图片上传 pipeline 加 WebP conversion：

好的任务写法：

```text
I need to add WebP conversion to our image upload pipeline.
Figure out where in the pipeline it should happen,
whether we need new dependencies,
and how to approach it.
```

这个 prompt 好在它没有直接命令“写代码”，而是让 Claude 先回答：

- 转换应该发生在 pipeline 的哪一步。
- 是否需要新依赖。
- 实现路径是什么。
- 可能的风险是什么。

四阶段拆解：

| 阶段 | 你要做什么 | Claude 要做什么 |
|---|---|---|
| Explore | 说明目标，让它读代码 | 找相关文件、理解现有结构 |
| Plan | 审查方案，提出补充 | 给出步骤、风险、验证方式 |
| Code | 批准后执行 | 修改文件、跑命令、修错误 |
| Commit | 人工复核后提交 | 总结变更、生成 commit message |

实战建议：

- 复杂任务先用 `Plan Mode`。
- 在计划阶段纠偏，成本最低。
- 验收标准要写清楚，比如测试通过、页面可打开、截图正确、命令无报错。
- 提交前可以让另一个 code review agent 检查风险。

## 7. Context Management：上下文管理

Context 是 Claude 的工作记忆。它读过的文件、命令输出、你写的消息、工具结果，都会占用 context window。

关键命令：

- `/context`：查看当前上下文占用情况。
- `/compact`：压缩当前会话，保留摘要，释放上下文空间。
- `/clear`：清空上下文，从头开始。

什么时候 compact：

- 当前功能还没做完。
- 会话很长，但仍然围绕同一个目标。
- 需要释放空间，但不能丢掉前面的工作结论。

什么时候 clear：

- 一个任务已经结束。
- 准备开始完全不同的新任务。
- 不希望上一个任务的假设污染新任务。

课程里有一个重要提醒：

> prompt 写得太短，长期反而更浪费上下文。

原因是你不给足约束，Claude 就需要自己到处搜索、猜测、读更多文件，反而消耗更多 context。更好的方式是：多写一两句关键约束，减少它无效探索。

## 8. MCP：连接外部工具和数据源

MCP 是 Model Context Protocol，用来让 Claude Code 连接外部工具和数据源。

典型用途：

- 连接 Linear，读取团队 issue。
- 连接 Context7，获取最新依赖文档。
- 连接数据库、生产力工具、内部系统。
- 通过远程 HTTP server 或本地 STDIO server 暴露工具。

MCP server 的 scope：

- `local`：只在当前项目对你可用。
- `user`：你所有项目都可用。
- `project`：写入 `.mcp.json`，团队成员拉代码后也能获得同样配置。

一个很重要的成本：

> MCP 工具定义会占用 context window。

如果你开了很多无关 MCP server，即使不使用，也可能挤占上下文。课程建议：

- 用 `/mcp` 查看已连接 server。
- 不用的 MCP 关掉。
- 如果工具有 CLI 替代，比如 GitHub 的 `gh`、AWS CLI，很多时候 CLI 更省 context。
- 对于不需要常驻 context 的能力，可以考虑用 skills：先只加载技能名和描述，需要时再展开。

这和你现在的 Codex 使用高度相关：飞书、领星、Sorftime、Shopify 等都可以看成不同层级的工具接入。关键不是接得越多越好，而是只在当前任务需要时加载。

## 9. Hooks：确定性控制

Hooks 的核心是确定性：它不是“建议 Claude 记得做”，而是“每次事件发生都执行”。

课程给的常见场景：

- 文件编辑后自动格式化。
- 记录所有执行过的命令，满足审计要求。
- 阻止危险操作，比如修改生产配置、执行 `rm -rf`、直接 commit 到 main。
- Claude 任务完成后发送通知。

Hooks 配置在 `settings.json` 里，可以按事件触发：

- `UserPromptSubmit`：用户提交 prompt 后、Claude 处理前。
- `PreToolUse`：工具调用前。
- `PostToolUse`：工具调用后。
- `Notification`：发送通知时。
- `Stop`：Claude 完成响应时。

两个最实用的案例：

### 自动格式化

在 `PostToolUse` 里匹配 edit/multi-edit。Claude 改完文件后，根据扩展名执行格式化：

- TypeScript 用 Prettier。
- Go 用 gofmt。
- Python 用 Ruff。

这样不会依赖 Claude 是否记得运行格式化命令。

### 阻止危险操作

在 `PreToolUse` 里读取工具名和输入。如果发现危险命令或危险路径：

- exit code `0`：允许执行。
- exit code `2`：阻止执行，并把错误信息反馈给 Claude。

适合硬规则：

- 禁止写生产配置目录。
- 禁止包含 `rm -rf` 的 bash 命令。
- 禁止直接 commit 到 main。

课程里的判断非常实用：

> 如果某件事必须每次都发生，不要写在 prompt 里，要写成 hook。

## 10. 这套课对你最有用的部分

结合你现在用 Codex 做个人助理、飞书、Obsidian、Amazon 研究、Lingxing 数据、Shopify 页面，最值得吸收的是这几条：

1. **把 agent 当执行者，不要当聊天框。**  
   目标、输入、输出、约束、验证方式要讲清楚。

2. **复杂任务先 Plan，不要直接 Code。**  
   尤其是涉及飞书表结构、Shopify 主题、仓库提交、批量改文件时，先读环境再出方案。

3. **把反复出现的偏好写进项目记忆。**  
   例如中文优先、敏感信息不展示、相对日期转绝对日期、飞书任务/日历优先等。

4. **上下文要主动管理。**  
   一个任务结束就清理；同一个任务很长就 compact；跨任务不要让旧假设污染新判断。

5. **工具接入要克制。**  
   MCP、skills、CLI 都是工具入口。当前任务需要什么就加载什么，避免上下文被无关工具占满。

6. **硬规则用 hook，软规则用 memory。**  
   “偏好”写进 AGENTS/CLAUDE.md；“必须执行/必须阻止”的动作写 hook。

## 11. 你可以马上练的 5 个任务

### 练习 1：给任意项目写项目记忆文件

目标：让 Codex/Claude Code 进入项目后不用重新理解规则。

写入：

- 项目用途。
- 常用命令。
- 输出路径。
- 禁止展示的敏感信息。
- 代码风格。
- 你最常纠正 agent 的 3 条规则。

### 练习 2：用 Plan Mode 改一个页面

prompt：

```text
请先只做探索和计划，不要改文件。
我想优化这个页面的手机端阅读体验。
请找出相关文件、当前布局问题、改动方案、风险和验证方法。
```

### 练习 3：让 agent 做一次代码审查

prompt：

```text
请站在 code review 角度看这次改动。
优先找 bug、回归风险、遗漏测试和不符合项目规则的地方。
不要先总结，先列问题。
```

### 练习 4：检查上下文污染

做完一个任务后问：

```text
当前会话里哪些上下文会影响下一个任务判断？
哪些应该写入长期记忆，哪些应该清除？
```

### 练习 5：把一个硬规则改成 hook

例如：

- 禁止提交 `.env`。
- 每次编辑后自动格式化。
- 每次生成 HTML 后检查文件存在和页面状态。

先让 agent 写方案，再决定是否真正落地。

## 12. 复习清单

学完这套课，你应该能回答：

- Claude Code 和普通 Claude Chat 最大差别是什么？
- agentic loop 的每一步分别做什么？
- 为什么复杂任务先用 Plan Mode？
- `CLAUDE.md` / `AGENTS.md` 应该写什么，不应该写什么？
- 什么时候 `/compact`，什么时候 `/clear`？
- MCP 为什么会占用 context？
- skill 和 MCP 的差异是什么？
- hook 适合解决什么问题？
- 哪些规则应该写在 memory，哪些应该写成 hook？

## 13. 后续学习建议

这套课很短，适合看两遍：

第一遍看概念：

- agentic loop
- context
- tools
- permissions
- memory
- MCP
- hooks

第二遍跟着做：

- 新建一个测试项目。
- 写一个项目记忆文件。
- 用 Plan Mode 做一次小功能。
- 让 agent 跑测试并修复问题。
- 最后让它生成 commit message。

真正掌握 Claude Code，不是记住每个命令，而是形成一个稳定工作方式：

> 讲清目标，限制权限，先计划，再执行，用测试验证，把经验沉淀进记忆或 hook。
