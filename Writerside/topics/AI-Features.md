# AI Features

<tldr>
    <p><b>Where</b>: the AI summary card on the PR timeline, the diff toolbar, and every comment editor.</p>
    <p><b>Turn it on</b>: <b>Enable AI features</b> in <ui-path>Settings | Tools | DevOps Lens | AI Settings</ui-path>, then add a provider.</p>
</tldr>

Optional AI helpers: PR summaries, full-diff reviews, code explanations, commit messages, PR title/description drafts,
grammar polish, and pipeline log analysis. **Bring your own provider** - OpenAI, Claude, Gemini, Ollama, or GitHub
Copilot - and route each feature wherever you like. Using an AI agent like Claude Code instead? The plugin can also
serve your Azure DevOps data to it as [MCP tools](MCP-Tools.md).

> Every AI call is **user-triggered**, and nothing can be sent anywhere until you add a provider: the master switch
> ships enabled, but with no usable provider configured the plugin makes **zero outbound AI calls** - the AI buttons just
> point you at the settings page. For exactly what's sent to a provider, see [](Privacy-and-Data.md).
> {style="note"}

> Until a provider is set up, the PR timeline shows a one-time **AI onboarding card** in the summary card's slot,
> linking straight to AI Settings. Dismiss it with **✕** and it never returns; configure a provider and it melts into the
> real [summary card](#tune-the-summary).
> {style="tip"}

## What the plugin can do with AI

| Feature                                   | What it does                                                                                                                                                     | Where                                                      |
|-------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| **Summarize Pull Request**                | Drafts a summary of the diff you can drop into the description.                                                                                                  | Timeline card / overflow menu                              |
| **Run AI Review**                         | Walks the diff and proposes inline review comments.                                                                                                              | Diff toolbar / changes-tree menu / overflow                |
| **Explain This File**                     | Streams a plain-English explanation of a file or selection.                                                                                                      | Right-click in the diff                                    |
| **Generate Commit Message with AI**       | Drafts a commit message from your staged changes.                                                                                                                | Commit tool window                                         |
| **Title and description**                   | Pre-fills the Create-PR form from your branch's diff.                                                                                                            | Create Pull Request form                                   |
| **Polish grammar &amp; spelling with AI** | Cleans up any comment or description in place.                                                                                                                   | Every comment editor                                       |
| **Analyze logs with AI**                  | Explains a finished pipeline run from its logs - root cause and fixes for a failure, a short summary for a green run. Sends only the relevant parts of the logs. | [Pipeline run job logs](Pipelines.md#analyze-logs-with-ai) |

![The AI summary card on a PR timeline](ai-summary-card.png){ width="700" border-effect="line" }

> **Run AI Review** proposes inline suggestions in the diff. Each one offers **Add to review** - which drops the AI's
> text into a new-comment editor on that line so you can edit it and queue it as a draft - or **Discard**, which parks it
> in a dismissed pool you can bring back with the **⟲ restore** control. A restore is free - never a paid re-run. While a
> suggestion card has focus, <shortcut>A</shortcut> adds and <shortcut>D</shortcut> discards it, and <shortcut>
> F8</shortcut> / <shortcut>⇧F8</shortcut> walk human comments, pending drafts, and AI suggestions in one reading order.
> **Nothing is posted to Azure DevOps until you submit your pending comments.**
> {style="tip"}

> A failed run doesn't fail silently: auth, quota, rate-limit, overload, context-too-large, and network errors each get
> their own actionable message with a one-click **Open AI Settings** jump - and a CLI sign-in failure offers to copy the
> fix command (for example `claude /login`).
> {style="note"}

### Review findings and history

**Run AI Review** opens a single **AI Reviews** tab in the **Pull Requests** tool window, scoped to that PR.
Select a file to open its next suggestion, or expand the file to choose a specific suggestion.
The tree uses the same folders, file icons and change colors as PR review. A blue dot marks items you have not viewed.
Hover over a suggestion, file, or folder to mark it viewed with the checkbox; click again to mark it not viewed.
Files and folders toggle all suggestions beneath them and show a partial check for mixed state. Space and the right-click menu work too.
Viewed state is saved per run, independently of dismissing or editing suggestions. New runs start unviewed.
Hover over the pull request or history selector for file coverage. The latest review is shown first; the history selector appears
when the PR has more than one run. Choose **All pull requests** to browse reviews across repositories in the current account.

- **Run AI Review** fetches the latest PR revision before starting. **Stop Review** cancels the active run.
- Each completed run records its provider/model, revision, completion time, and file coverage. Skipped files are counted
  explicitly; no findings does not mean every changed file was reviewed.
- **Dismiss Finding** and **Restore Finding** update the same suggestions shown in the diff. Restoring does not call AI.
- **Opened for editing** means a suggestion was handed to the ordinary comment editor. It does not mean the comment
  was saved or posted. Submit comments through the normal PR review workflow.
- Previous runs remain available. Historical findings open a read-only diff of the revision that was reviewed.

History is saved locally in the IDE cache and survives an IDE restart. It is bounded to the newest eight runs per PR
and 80 runs overall, subject to cache size limits. It is not uploaded to Azure DevOps or synced with IDE settings.

### Is the AI review still current?

The timeline sidebar keeps a compact **AI review** status with **Open AI Reviews** and **Re-run**. A source or target
revision change marks the saved review as outdated. **Re-run** starts a new, billable review; opening history and
restoring dismissed findings do not.

The sidebar section is hidden when AI is disabled or unconfigured. Saved history is still accessible through
the **AI Reviews** button in the Pull Requests tool-window toolbar.

### Tune the summary {id="tune-the-summary" collapsible="true"}

The **Summary settings** gear in the top-right corner of the **AI summary** card opens a popup that controls how the
summary is generated:

| Control                            | Options                                                                                                                                                                     |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Generate automatically on open** | Checkbox - off by default. When on, the card drafts a summary as soon as the PR opens.                                                                                      |
| **Verbosity**                      | Slider: **Brief** · **Neutral** · **Verbose**.                                                                                                                              |
| **Formality tone**                 | Slider: **Informal** · **Neutral** · **Formal**.                                                                                                                            |
| **Personality**                    | Free-text - an optional persona, e.g. "a slightly sarcastic principal engineer".                                                                                            |
| **Customization prompt**           | Free-text - leave blank to use the default. It's the same override as **Prompt templates → Pull Request summary** in AI Settings, so edits in either surface stay in sync. |

There's no Save button - edit the controls and dismiss the popup to apply.

![The Summary settings popup off the AI summary card's gear](ai-summary-settings-popup.png){ width="520" border-effect="line" }

## Configure providers

![The AI Settings page: providers and per-feature routing](configure-providers.png){ width="720" border-effect="line" thumbnail="true" }

<procedure title="Add an AI provider">
    <step>Open <ui-path>Settings | Tools | DevOps Lens | AI Settings</ui-path> and turn on <b>Enable AI features</b> (the master switch).</step>
    <step>Add a provider in the <b>Model providers</b> table. Each row is one provider instance with a <b>Provider</b>,
        <b>Model</b>, and <b>Active</b> column; the first active row is the default.</step>
    <step>In the <b>Add AI Provider</b> dialog, pick one of the five families (below) and a <b>Model</b>.</step>
    <step>On a reasoning model, optionally pick an <b>Effort</b> (below). Leave it on <b>Model default</b> if you're
        not sure.</step>
    <step>Pick the provider's <b>mode</b> - HTTP API or CLI (below) - and fill in what it asks for.</step>
    <step>Use <b>Test Connection</b> in the dialog to confirm the provider works before saving.</step>
</procedure>

The **Add AI Provider** dialog offers five families:

| Family             | Notes                                                                          |
|--------------------|--------------------------------------------------------------------------------|
| **OpenAI**         | GPT models. Works with any OpenAI-compatible base URL (Azure OpenAI, vLLM, …). |
| **Claude**         | Anthropic Claude models.                                                       |
| **Gemini**         | Google Gemini models.                                                          |
| **Ollama**         | Local models - free, no key.                                                   |
| **GitHub Copilot** | Uses your Copilot subscription (CLI only).                                     |

> The **Model** dropdown in the Add/Edit dialog populates itself: it shows a bundled suggested list instantly, then
> refreshes from a live query to the provider (for example OpenAI and Claude's `/v1/models`) so newly-shipped models
> appear without a plugin update. The live list is cached for about 30 minutes; it refreshes on every dialog open and
> whenever you change the family or mode, so there's no manual refresh button. Discovery needs a saved key - until one is
> entered the dropdown falls back to the suggested list. The field stays editable, so you can always type a model id by
> hand.
> {style="note"}

### Effort {id="effort"}

Reasoning models can be told how hard to think before answering. When the model you picked supports it, an **Effort**
dropdown appears under **Model**:

| Choice                          | What it means                                                                                            |
|---------------------------------|------------------------------------------------------------------------------------------------------------|
| **Model default (recommended)** | Sends no effort setting - the provider's own default. This is the default choice.                          |
| A lower level                   | Faster and cheaper, with shallower reasoning. Good for summaries and commit messages.                      |
| A higher level                  | Slower and more expensive, with deeper reasoning. Worth it for an AI review of a large or subtle change.   |

Which levels are offered depends on the family and the exact model - newer models expose more of them, and models with
no reasoning control don't show the row at all. Effort is set **per provider instance**, so a common setup is two rows
against the same family: a fast, low-effort one for summaries and a slower, high-effort one routed at AI Review under
[Provider per feature](#route-features-to-providers).

Higher effort costs more, because reasoning tokens are billed. See [Caching, cost, and limits](#caching-cost-and-limits).

## Modes

Most families run in one of two **modes**:

<tabs>
    <tab title="HTTP API (use an API key)">
        <p>Paste a key; optionally set an <b>API URL</b> to point at a custom endpoint. Keys are stored in the IDE
            keychain (PasswordSafe).</p>
    </tab>
    <tab title="CLI (use the local command-line tool)">
        <p>No key; the local binary handles its own auth. The lowest-friction path, but you take on the CLI vendor's
            terms.</p>
    </tab>
</tabs>

## Route features to providers

The **Provider per feature** panel pins each feature to a specific instance - handy for sending cheap features to a
small model and heavy reviews to a smart one:

```
Pull request summary  → [Default ▾]
Code review           → [Default ▾]
Title and description → [Default ▾]   (also used by Generate Commit Message)
Explain code          → [Default ▾]
```

Leave a row on **Default** to use the first active provider. You can add the **same family more than once** (e.g. two
OpenAI rows, a cheap model and a smart one) and route to each independently.

### Prompt templates

The **Prompt templates** panel lets you edit the system prompt behind each feature. Editing a prompt invalidates cached
responses for it.

## Pick the response language

Two settings in the **AI features** group, right under the master switch:

| Setting                                                                     | Default |
|-----------------------------------------------------------------------------|---------|
| **Response language**                                                    | Auto    |
| **Also use it for pull request titles, descriptions and commit messages** | Off     |

**Response language** is the language the model writes summaries, code explanations, review notes, and pipeline log
analysis in. **Auto** follows the IDE language; polishing text you wrote yourself always keeps the language you wrote
it in. The checkbox beneath is a separate opt-in because PR titles, descriptions, and commit messages land in git
history and on the pull request, where your team's convention matters more than your IDE's language - what you read
inside the IDE follows the dropdown either way.

## Caching, cost, and limits

AI responses are cached **per PR + per commit SHA** (toggle: **Cache AI responses per commit SHA**, on by default). A
cache hit returns instantly with no API call; a new commit or an edited prompt invalidates it. Force a refresh with
**Clear AI Response Cache** in **Advanced**.

You pay your provider's bill for the tokens you use. To keep usage down:

- Route cheap features (commit message, title) to a small model via per-feature routing.
- Lower **Max diff size** in **Advanced** to truncate big diffs before they're sent.
- Keep the cache on so re-opening a PR doesn't re-bill you.

Some savings are automatic: lockfiles (`package-lock.json`, `yarn.lock`, `uv.lock`, …), minified and generated files,
binaries, and build-output folders are stripped from every AI diff before it's sent; a renamed file sends only its
actual edits, and a deleted file sends a one-line note instead of its contents.

> Provider quota and usage-limit errors come straight from the provider - the plugin classifies them and shows clear,
> actionable wording rather than failing silently. It doesn't add its own rate limits or retries.
> {style="note"}

## Keep everything local, or off

- **Local inference:** route every feature at an **Ollama** instance on `localhost` - no code leaves your machine.
- **Off entirely:** uncheck **Enable AI features**. Every AI affordance disappears from menus and toolbars, and the
  plugin makes zero outbound AI calls.

## AI tool-window actions

Open **AI Reviews** from the AI button beside **Statistics** in the Pull Requests tool window. Its toolbar keeps **Run AI Review** and **Open Pull Request** visible while details load. Use **Choose pull request…** to search for a PR, including one with no review history, then run its review directly here. A cold action waits for the PR and reports a loading failure instead of silently doing nothing.

AI log analysis currently covers the **whole pipeline run**, including when opened from a job header or a step’s **⋮** menu. Per-step and per-group analysis is deferred. Each step menu also provides **Copy Link** and **Open in Browser**, both targeting that step. The AI button beside **Statistics** stays available while connected. In **AI Analysis**, use **Choose Existing Run…** to select a finished run, **Analyze Logs** for a fresh analysis, **Stop** to cancel, and the history selector to reopen a retained result. Selecting history makes no AI call; closing the tab does not stop generation. History follows the same retention and clear-cache settings as PR reviews. These actions never start a new pipeline run.

In the PR and existing-run choosers, **Enter** selects the highlighted item for AI review or analysis; **Shift+Enter** opens its PR timeline or pipeline run directly. The existing-run chooser uses the same compact rows and popup background as pipeline quick search.
