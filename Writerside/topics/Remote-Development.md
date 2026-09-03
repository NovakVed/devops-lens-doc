# Remote Development

<tldr>
    <p><b>What</b>: %product% works in JetBrains Remote Development - the full plugin, driven from the thin client.</p>
    <p><b>Install it</b>: on the <b>remote host</b> only. Nothing to install on your laptop.</p>
    <p><b>Setup</b>: none. Sign in once on the host and everything behaves as it does locally.</p>
</tldr>

When you work through **JetBrains Gateway** - the IDE backend runs on a remote machine and you drive it from the
JetBrains Client on your own computer - %product% comes along. This is often called **split mode**, because the IDE is
split across two machines: the backend does the work, the client draws the UI.

## What you get in the client

Everything. The plugin is not reduced to a read-only view or to a subset of its features in a remote session:

| Surface                       | In a remote session                                                          |
|-------------------------------|------------------------------------------------------------------------------|
| **Pull Requests** tool window | Full list, filters, search, and the detail view with its timeline and tabs.  |
| **Pipelines** tool window     | Full run list, stage graph, job logs, tests, coverage, and approvals.        |
| Review editors                | Diffs, inline comment threads, and the in-editor review overlay.             |
| Branch widgets                | The main-toolbar PR badge and the status-bar widget.                         |
| Menus and shortcuts           | The **VCS** menu entries, context menus, and every keybinding.               |
| Settings                      | The **DevOps Lens** settings pages and all of their sub-pages.               |

Because the backend holds the connection, network behavior follows the **host**, not your laptop: the host reaches your
Azure DevOps organization, resolves your on-prem server name, and uses the host's proxy and certificates. A self-hosted
Azure DevOps Server that is only reachable from inside a company network works in a remote session for exactly that
reason - the machine that has to see it is the one running the backend.

## Install and sign in

<procedure title="Set up a remote session">
    <step>Connect to the remote machine with JetBrains Gateway as you normally would.</step>
    <step>In the remote IDE, install %product% from <ui-path>Settings | Plugins | Marketplace</ui-path>. Install it on
        the <b>host</b>; the client picks it up automatically.</step>
    <step>Sign in from the remote IDE. Tokens are stored in the <b>host's</b> credential store, not on your laptop.</step>
</procedure>

See [](Installation.md) and [](Authentication.md) for the details of each step - they are the same as for a local IDE.

> **Only the host needs the plugin.** Remote Development pairs the two sides for you. If you also have %product%
> installed locally for local projects, that copy simply sits idle during a remote session.
> {style="note"}

## Things worth knowing

- **Your credentials live on the host.** Signing in inside a remote session stores the token in the remote machine's
  credential store. A local sign-in does not carry over, and vice versa.
- **Browser hand-offs open on your machine.** Actions that open the Azure DevOps website - **Open in Browser**, copied
  links, the OAuth sign-in flow - hand the URL to the client, so the page opens in your own browser.
- **Background work runs on the host.** Background refresh, [live updates](Pull-Requests.md#live-updates) and
  notifications are produced on the backend and shown in whichever client is attached.

## If the tool windows don't appear

| Symptom                                   | Check                                                                                          |
|-------------------------------------------|-------------------------------------------------------------------------------------------------|
| No **Pull Requests** stripe in the client | The project on the host has no Azure DevOps Git remote - see [](Git-Integration.md).           |
| The plugin shows as disabled              | Confirm it is installed on the **host**, and that the host IDE is %min_ide_version% or newer.  |
| Everything is there but empty             | You are signed in locally but not on the host. Sign in from inside the remote session.         |

For anything else, see [](Troubleshooting.md).
