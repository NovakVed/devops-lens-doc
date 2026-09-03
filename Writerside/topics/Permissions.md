# Permissions

<tldr>
    <p><b>Microsoft sign-in:</b> there is no Full/Standard choice. %product% opens Microsoft in your browser and requests one audited set of app-required permissions.</p>
    <p><b>Personal Access Token:</b> select the same capabilities manually. You do not need to choose <b>Full access</b>.</p>
</tldr>

%product% requests only permissions exercised by a feature in the app. The set is intentionally large enough for every
DevOps Lens feature—pull-request review, work-item linking, pipelines, approvals, tests, environments, and agents—but it
is not unrestricted access to your Azure DevOps account.

Microsoft still shows its normal consent page. That page is the authoritative place to review the requested access
before accepting it; DevOps Lens no longer puts a second permission-tier dialog in front of it.

> Azure DevOps scopes inherit narrower scopes. For example, `vso.code_write` already includes `vso.code`, and
> `vso.test` includes `vso.profile`. DevOps Lens does not request those inherited scopes a second time. See Microsoft's
> [Azure DevOps OAuth scope reference](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/oauth?view=azure-devops#scopes).
> {style="note"}

## App-required permissions

OAuth requests the scope codes below automatically. When creating a PAT, select the matching entries in the Azure DevOps
token form.

| PAT setting                             | OAuth scope                         | Why DevOps Lens uses it                                                                                       |
|-----------------------------------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------|
| **Code → Read &amp; write + Status**    | `vso.code_write`, `vso.code_status` | Read Git/PR data; comment, vote, complete and update PRs; update branches and labels; read PR/commit checks. |
| **Identity → Read**                     | `vso.identity`                      | Search and resolve people for @-mentions.                                                                     |
| **Work Items → Read &amp; write**       | `vso.work_write`                    | Read linked work items; link/unlink them; optionally update state when completing a PR.                       |
| **Project and Team → Read**             | `vso.project`                       | Read teams for the team-assigned part of the default **Mine** PR view.                                        |
| **Security → Manage**                   | `vso.security_manage`               | Check your own branch-policy bypass and pipeline permissions. Azure exposes no read-only scope for this check. |
| **Build → Read &amp; execute**          | `vso.build_execute`                 | Read runs, logs, artifacts, definitions and coverage; queue/cancel/retry runs; edit definitions and leases.   |
| **Pipeline Resources → Use**            | `vso.pipelineresources_use`         | Approve or reject requests to use protected pipeline resources.                                               |
| **Test Management → Read**              | `vso.test`                          | Read test runs, results and coverage. This also includes profile access used for avatars.                     |
| **Environment → Read &amp; manage**     | `vso.environment_manage`            | Read environments and deployment records. Azure has no read-only environment scope; it also includes agent-pool access used by the Agents view. |

OAuth also asks Microsoft for `offline_access`, which lets the plugin refresh the session without making you sign in on
every IDE restart. It does not grant access to additional Azure DevOps data.

![The Log In to Azure DevOps dialog listing the permissions a PAT must grant](sign-in-with-token.png){ width="560" border-effect="line" }

## Why some labels say “Manage”

Azure DevOps does not provide a narrower OAuth scope for every read operation DevOps Lens performs. In particular, the
permission checks and environment APIs require **Security → Manage** and **Environment → Read &amp; manage**. DevOps Lens
uses those grants only for the features described above; the labels do not mean the app requests Azure DevOps **Full
access** or the broad `user_impersonation` scope.

## Fixing a missing PAT permission

PAT permissions are fixed when the token is created; the plugin cannot add one afterward.

<procedure title="Re-authenticate with a new PAT">
    <step>In Azure DevOps, create a new token with every entry in the table above.</step>
    <step>In <ui-path>Settings | Tools | DevOps Lens</ui-path>, remove the old account with <b>−</b>.</step>
    <step>Add it again with <b>+</b> → <b>Log In with Token…</b> and paste the new token.</step>
</procedure>

For Microsoft sign-in, simply remove the account and sign in again. The current app-required set is requested
automatically—there is no permission-tier chooser.

See [](Authentication.md) for the full sign-in walkthrough.
