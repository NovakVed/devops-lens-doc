# AI 功能

<tldr>
    <p><b>位置</b>：PR 时间线上的 AI 摘要卡片、差异工具栏以及每个评论编辑器。</p>
    <p><b>如何开启</b>：在 <ui-path>Settings | Tools | DevOps Lens | AI Settings</ui-path> 中开启 <b>Enable AI features</b>，然后添加服务商。</p>
</tldr>

可选的 AI 助手：PR 摘要、完整差异审查、代码解释、提交消息、PR 标题/描述草稿、语法润色以及管道日志分析。 **自带服务商** -
OpenAI、Claude、Gemini、Ollama 或 GitHub Copilot - 并可将每项功能路由到你喜欢的任意服务商。在使用 Claude Code 这类 AI
代理？插件还可以把你的 Azure DevOps 数据作为 [MCP 工具](MCP-Tools-zh.md) 提供给它。

> 每一次 AI 调用都是 **由用户触发的**，而且在你添加服务商之前，任何内容都不可能被发送到任何地方：总开关出厂即为启用，但只要没有配置可用的服务商，插件就
> **不会发出任何对外的 AI 调用** - AI
> 按钮只会把你引导到设置页面。关于具体发送给服务商的内容，请参阅[](Privacy-and-Data-zh.md)。
> {style="note"}

> 在设置好服务商之前，PR 时间线会在摘要卡片的位置显示一张一次性的 **AI 引导卡片**，直接链接到 AI Settings。用 **✕**
> 关闭后它就再也不会出现；配置好服务商后，它会化作真正的[摘要卡片](#tune-the-summary)。
> {style="tip"}

## 插件能用 AI 做什么

| 功能                                      | 作用                                                                                                        | 位置                                                       |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| **Summarize Pull Request**                | 起草一份差异摘要，可直接放入描述中。                                                                        | 时间线卡片 / 溢出菜单                                      |
| **Run AI Review**                         | 遍历差异并提出内联审查评论。                                                                                | 差异工具栏 / 变更树菜单 / 溢出菜单                         |
| **Explain This File**                     | 流式输出对某个文件或选区的通俗英文解释。                                                                    | 在差异中右键单击                                           |
| **Generate Commit Message with AI**       | 根据你暂存的更改起草一条提交消息。                                                                          | Commit 工具窗口                                            |
| **Title and description**                   | 根据分支的差异预填 Create-PR 表单。                                                                         | Create Pull Request 表单                                   |
| **Polish grammar &amp; spelling with AI** | 就地清理任意评论或描述。                                                                                    | 每个评论编辑器                                             |
| **Analyze logs with AI**                  | 根据日志解释一次已结束的管道运行 - 失败时给出根本原因与修复建议，成功时给出简短摘要。只发送日志的相关部分。 | [管道运行的作业日志](Pipelines-zh.md#analyze-logs-with-ai) |

![PR 时间线上的 AI 摘要卡片](ai-summary-card-zh.png){ width="700" border-effect="line" }

> **Run AI Review** 会在差异中提出内联建议。每条建议都提供 **Add to review** - 将 AI
> 的文本放入该行的新评论编辑器中，方便你编辑并将其作为草稿排队 - 或 **Discard**，将其放入一个已弃置池，随时可用 **⟲ restore**
> 控件找回（恢复是免费的 - 绝不会触发付费的重新运行）。当某张建议卡片获得焦点时，<shortcut>A</shortcut> 采纳、<shortcut>
> D</shortcut> 丢弃该建议，而 <shortcut>F8</shortcut> / <shortcut>⇧F8</shortcut> 会按同一阅读顺序遍历人工评论、待提交草稿和
> AI 建议。 **在你提交待处理评论之前，不会有任何内容发布到 Azure DevOps。**
> {style="tip"}

> 运行失败不会无声无息：鉴权、配额、速率限制、服务过载、上下文过大和网络错误各自都有对应的可操作提示消息，并带有一键跳转的
> **Open AI Settings** - CLI 登录失败还会提供复制修复命令的选项（例如 `claude /login`）。
> {style="note"}

### 审阅发现和历史 {id="review-findings-and-history"}

运行 **Run AI Review** 后，**Pull Requests** 工具窗口中的 **AI Reviews** 标签页会打开，并限定到当前 PR。
选择文件可打开下一条建议，展开文件可选择具体建议。文件树使用与 PR 审阅相同的文件夹结构、文件图标和更改颜色；蓝点表示尚未查看的项目。
将鼠标移到建议、文件或文件夹上，即可通过复选框切换已查看/未查看。文件和文件夹会切换其下所有建议，部分已查看时显示半选状态。也可使用 Space 键或右键菜单。
查看状态按运行保存，与忽略或编辑建议相互独立。新的运行从未查看开始。将鼠标悬停在 PR 或历史选择器上可查看文件覆盖范围。默认显示最新审阅；同一 PR 有多次运行时，会出现历史选择器。
选择 **All pull requests** 可查看当前账号下跨仓库的审阅。

- **Run AI Review** 会先获取 PR 的最新修订版本。**Stop Review** 可停止正在进行的审阅。
- 每次运行记录提供商、模型、修订版本、完成时间及文件覆盖范围，并明确显示跳过的文件数。
- **Dismiss Finding** 和 **Restore Finding** 会同步更新 diff 中的建议。恢复不会再次调用 AI。
- **Opened for editing** 表示建议已交给普通评论编辑器，不代表评论已经保存或发布。
- 历史发现会在当时所审阅版本的只读 diff 中打开。

历史记录保存在本地 IDE 缓存中，重启后仍可查看。在缓存大小限制内，每个 PR 最多保留最近 8 次运行，总计最多 80 次。
这些记录不会上传到 Azure DevOps，也不会随 IDE 设置同步。

### AI 审阅是否仍然最新？ {id="is-the-ai-review-still-current"}

时间线的 **AI review** 区块保留简洁的状态，以及 **Open AI Reviews** 和 **Re-run** 操作。
源分支或目标分支的修订版本变化后，保存的审阅会标为过期。**Re-run** 会启动一次新的付费审阅。
查看历史和恢复已忽略的发现不会调用 AI。

AI 已关闭或尚未配置时，此区块会隐藏。已保存的历史仍可通过 Pull Requests 工具窗口工具栏中的**AI Reviews** 按钮 打开。

### 调整摘要 {id="tune-the-summary"}

**AI summary** 卡片右上角的 **Summary settings** 齿轮会打开一个弹窗，用于控制摘要的生成方式：

| 控件                               | 选项                                                                                                                                             |
|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| **Generate automatically on open** | 复选框 - 默认关闭。开启后，PR 一打开卡片就会起草摘要。                                                                                           |
| **Verbosity**                      | 滑块：**Brief** · **Neutral** · **Verbose**。                                                                                                    |
| **Formality tone**                 | 滑块：**Informal** · **Neutral** · **Formal**。                                                                                                  |
| **Personality**                    | 自由文本 - 可选的人设，例如"一位略带讽刺的首席工程师"。                                                                                          |
| **Customization prompt**           | 自由文本 - 留空则使用默认值。它与 AI Settings 中的 **Prompt templates → Pull Request summary** 是同一个覆盖项，因此在任一处的编辑都会保持同步。 |

没有保存按钮 - 编辑控件后关闭弹窗即可应用。

![从 AI 摘要卡片齿轮弹出的 Summary settings 弹窗](ai-summary-settings-popup-zh.png){ width="520" border-effect="line" }

## 配置服务商

打开 <ui-path>Settings | Tools | DevOps Lens | AI Settings</ui-path> 并开启 **Enable AI features**（总开关）。然后在
**Model providers** 表中添加服务商。

![AI Settings 页面：服务商与按功能路由](configure-providers.png){ width="720" border-effect="line" thumbnail="true" }

每一行代表一个服务商实例，包含 **Provider**、 **Model** 和 **Enabled** 列；第一个启用的行即为默认。 **Add AI Provider**
对话框提供五个系列：

| 系列               | 说明                                                                           |
|--------------------|--------------------------------------------------------------------------------|
| **OpenAI**         | GPT 模型。可与任意兼容 OpenAI 的 base URL 配合使用（Azure OpenAI、vLLM，……）。 |
| **Claude**         | Anthropic Claude 模型。                                                        |
| **Gemini**         | Google Gemini 模型。                                                           |
| **Ollama**         | 本地模型 - 免费，无需密钥。                                                    |
| **GitHub Copilot** | 使用你的 Copilot 订阅（仅限 CLI）。                                            |

> Add/Edit 对话框中的 **Model** 下拉框会自动填充：它会立即显示一份内置的建议列表，然后通过对服务商的实时查询进行刷新（例如
> OpenAI 和 Claude 的 `/v1/models`），这样新发布的模型无需更新插件即可出现。实时列表会缓存约 30
> 分钟；它会在每次打开对话框以及你更改系列或模式时刷新，所以没有手动刷新按钮。发现功能需要一个已保存的密钥 -
> 在输入密钥之前，下拉框会回退到建议列表。该字段始终可编辑，因此你随时可以手动输入模型 id。
> {style="note"}

大多数系列以两种 **模式**之一运行：

- **HTTP API (use an API key)** - 粘贴一个密钥；可选择设置 **API URL** 以指向自定义端点。密钥存储在 IDE
  密钥链（PasswordSafe）中。
- **CLI (use the local command-line tool)** - 无需密钥；本地二进制文件自行处理鉴权。这是阻力最小的路径，但你需要接受 CLI
  供应商的条款。

在对话框中使用 **Test Connection** 以在保存前确认服务商可用。

## 将功能路由到服务商

**Provider per feature** 面板将每项功能固定到特定实例 - 便于将廉价功能发送给小模型，而将繁重的审查发送给智能模型：

```
Pull request summary  → [Default ▾]
Code review           → [Default ▾]
Title and description → [Default ▾]   (also used by Generate Commit Message)
Explain code          → [Default ▾]
```

将某一行保留为 **Default** 即可使用第一个启用的服务商。你可以 **多次添加同一系列**（例如两个 OpenAI
行，一个廉价模型和一个智能模型），并分别独立路由。

### Prompt templates

**Prompt templates** 面板让你可以编辑每项功能背后的系统提示。编辑某个提示会使其缓存的响应失效。

## 选择响应语言

**AI features** 分组中、主开关正下方的两项设置：

| 设置                                                                        | 默认值 |
|-----------------------------------------------------------------------------|--------|
| **Response language**                                                    | Auto   |
| **Also use it for pull request titles, descriptions and commit messages** | 关闭   |

**Response language** 是模型撰写摘要、代码解释、审查意见和流水线日志分析所用的语言。 **Auto** 跟随 IDE 语言；润色你自己
撰写的文本时，始终保持你所使用的语言。下方的复选框是单独的选择加入项，因为 PR 标题、描述和提交信息会进入 git
历史并出现在拉取请求上，在那里团队的约定比你 IDE 的语言更重要——无论如何，你在 IDE 内阅读的内容都跟随该下拉框。

## 缓存、成本与限制

AI 响应按 **每个 PR + 每个提交 SHA** 缓存（开关： **Cache AI responses per commit SHA**，默认开启）。缓存命中会立即返回且不产生
API 调用；新的提交或编辑过的提示会使其失效。可通过 **Advanced** 中的 **Clear AI Response Cache** 强制刷新。

你为所使用的 token 向服务商付费。为了控制用量：

- 通过按功能路由，将廉价功能（提交消息、标题）路由到小模型。
- 在 **Advanced** 中降低 **Max diff size**，在发送前截断大型差异。
- 保持缓存开启，这样重新打开 PR 不会再次向你计费。

部分节省是自动完成的：锁定文件（`package-lock.json`、`yarn.lock`、`uv.lock` 等）、压缩（minified）和自动生成的文件、二进制文件以及构建输出文件夹会在发送前从每个
AI 差异中剔除；重命名的文件只发送其实际编辑内容，已删除的文件只发送一行说明而不是其内容。

> 服务商配额和用量限制错误直接来自服务商 - 插件会对其分类并显示清晰、可操作的措辞，而不是无声地失败。它不会添加自己的速率限制或重试。
> {style="note"}

## 保持一切本地，或完全关闭

- **本地推理：** 将每项功能都路由到 `localhost` 上的 **Ollama** 实例 - 没有任何代码离开你的机器。
- **完全关闭：** 取消勾选 **Enable AI features**。所有 AI 功能都会从菜单和工具栏中消失，插件也不会发出任何对外的 AI 调用。


## AI 工具窗口操作

通过 Pull Requests 工具窗口中 **Statistics** 旁的 AI 按钮打开 **AI Reviews**。加载详情时，**Run AI Review** 和 **Open Pull Request** 仍然可见。使用 **选择拉取请求…** 搜索 PR，包括尚无审查历史的 PR，然后直接在此运行审查。尚未加载的 PR 会等待加载，失败时显示错误，而不是无响应。

AI 日志分析目前始终覆盖**整个流水线运行**，包括从作业标题栏或步骤的 **⋮** 菜单打开时。按步骤或分组分析将留待后续实现。每个步骤菜单还提供指向该步骤的**复制链接**和**在浏览器中打开**。连接时始终可使用 **Statistics** 旁的 AI 按钮。在 **AI 分析** 中使用**选择已有运行…**、**分析日志**、**停止**及历史选择器。查看历史不会调用 AI，关闭标签页也不会停止分析。历史遵循与 PR 审查相同的保留和清除缓存设置。这些操作不会启动新的流水线运行。

在 PR 和已有运行的选择器中，**Enter** 将高亮项选为 AI 审查或分析对象，**Shift+Enter** 直接打开其 PR 时间线或流水线运行。已有运行选择器采用与流水线快速搜索相同的紧凑行和弹窗背景。
