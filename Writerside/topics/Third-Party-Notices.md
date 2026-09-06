# Third-Party Notices

%product% is proprietary software, but it ships a small amount of open-source code and artwork. This page lists those
components and their licenses. The authoritative copy travels inside the plugin itself: the main jar carries
`THIRD-PARTY-NOTICES`, `APACHE-LICENSE-2.0.txt`, and `MIT-LICENSE.txt` under `META-INF/`.

The plugin bundles no third-party libraries. The libraries it runs on (Kotlin, kotlinx, Ktor, MigLayout) are provided
by the JetBrains IDE it is installed into, under that IDE's own third-party notices.

## Apache License 2.0

The following components are copied or derived from
[IntelliJ Community](https://github.com/JetBrains/intellij-community), Copyright 2000-2026 JetBrains s.r.o. and
contributors, licensed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0). Each
file in the plugin carries an attribution comment naming its upstream source. IntelliJ Community's NOTICE file reads:

```text
This software includes code from IntelliJ IDEA
Copyright (C) JetBrains s.r.o.
https://www.jetbrains.com/idea/
```

| Component in %product% | Upstream source | Changes |
|---|---|---|
| Base and head branch picker in Create Pull Request | git4idea `MergeDirectionComponentFactory.kt` | Renamed and moved; minimum popup width; renderer and branch-model adjustments |
| Generation-state model behind the AI summary card | GitHub plugin `GHPRAISummaryViewModel.kt` | Reformatted |
| Viewed-state renderer in the pull request changes tree | collaboration-tools `CodeReviewProgressRenderer.kt` | Adapted to the plugin's own review state; tri-state folder checkbox; unresolved-comment badge |
| Comment-bubble geometry of the unresolved badge | `expui/comment.svg` | Traced into a path; colored amber |
| Pull Requests tool-window icon | GitHub plugin `expui/pullRequests.svg` | None |
| Neutral and merged pull request icons | collaboration-tools `expui/pullRequestOpen.svg` | Recolored; the merged variant is redrawn |
| Sort-direction icons | `expui/runConfigurations/sortByDuration.svg` | The ascending variant is mirrored |
| Statistics icon | `expui/nodes/library.svg` | Interior fills removed |
| Comment-composer formatting icons (heading, bold, italic, code, link, lists) | Markdown plugin `expui/editorActions/*.svg` | None |

## MIT License

- **GitHub Octicons** rocket artwork, adapted for the Pipelines tool-window icon. Copyright (c) GitHub Inc.
  Source: [github.com/primer/octicons](https://github.com/primer/octicons). License: [MIT](https://opensource.org/license/mit).

## CC0 1.0 Universal

- **Simple Icons** provider marks used to identify Claude, GitHub Copilot, Google Gemini, Ollama, and OpenAI in the
  AI provider selector. Source: [github.com/simple-icons/simple-icons](https://github.com/simple-icons/simple-icons).
  License: [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/).

CC0 covers copyright only. The provider names and marks belong to their respective owners and are used only to
identify user-selected compatible services.

## Trademarks

Microsoft, Azure, and Azure DevOps are trademarks of the Microsoft group of companies. JetBrains, IntelliJ, and the
names of JetBrains products are trademarks of JetBrains s.r.o. GitHub is a trademark of GitHub Inc. All other
trademarks belong to their respective owners. No affiliation, sponsorship, certification, or endorsement is implied.
