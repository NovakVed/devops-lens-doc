# 权限

<tldr>
    <p><b>Microsoft 登录：</b>不再有 Full/Standard 选择。%product% 会在浏览器中打开 Microsoft，并且只请求一组经过审核、应用实际需要的权限。</p>
    <p><b>个人访问令牌：</b>手动选择相同的功能即可，无需选择 <b>Full access</b>。</p>
</tldr>

%product% 只请求应用功能实际使用的权限。这组权限足以支持 DevOps Lens 的全部功能——拉取请求审查、工作项关联、流水线、审批、测试、环境和代理——但并不等同于对 Azure DevOps 账户的无限制访问。

Microsoft 的标准同意页面仍会显示。它是你接受之前查看所请求访问权限的权威位置；DevOps Lens 不再在它之前额外显示权限层级对话框。

> Azure DevOps 作用域会继承更窄的作用域。例如，`vso.code_write` 已包含 `vso.code`，`vso.test` 已包含 `vso.profile`。DevOps Lens 不会重复请求这些已继承的作用域。另请参阅 Microsoft 的 [Azure DevOps OAuth 作用域参考](https://learn.microsoft.com/zh-cn/azure/devops/integrate/get-started/authentication/oauth?view=azure-devops#scopes)。
> {style="note"}

## 应用所需权限

OAuth 会自动请求下列作用域代码。创建 PAT 时，请在 Azure DevOps 令牌表单中选择对应项目。

| PAT 设置                                    | OAuth 作用域                         | DevOps Lens 使用它的原因 |
|---------------------------------------------|--------------------------------------|--------------------------|
| **Code → Read &amp; write + Status**        | `vso.code_write`, `vso.code_status`  | 读取 Git/PR 数据；评论、投票、完成和更新 PR；更新分支和标签；读取 PR/提交检查。 |
| **Identity → Read**                         | `vso.identity`                       | 为 @提及搜索和解析人员。 |
| **Work Items → Read &amp; write**           | `vso.work_write`                     | 读取关联工作项、关联/取消关联，以及在完成 PR 时可选地更新状态。 |
| **Project and Team → Read**                 | `vso.project`                        | 读取团队信息，以支持默认 **Mine** PR 视图中分配给团队的部分。 |
| **Security → Manage**                       | `vso.security_manage`                | 检查你自己的分支策略绕过权限和流水线权限。Azure 没有针对此检查的只读作用域。 |
| **Build → Read &amp; execute**              | `vso.build_execute`                  | 读取运行、日志、构件、定义和覆盖率；排队/取消/重试运行；编辑定义和保留租约。 |
| **Pipeline Resources → Use**                | `vso.pipelineresources_use`          | 批准或拒绝受保护流水线资源的使用请求。 |
| **Test Management → Read**                  | `vso.test`                           | 读取测试运行、结果和覆盖率；还包含头像所需的个人资料访问。 |
| **Environment → Read &amp; manage**         | `vso.environment_manage`             | 读取环境和部署记录。Azure 没有只读环境作用域；它还包含 Agents 视图使用的代理池访问。 |

OAuth 还会向 Microsoft 请求 `offline_access`，以便插件在 IDE 重启后刷新会话，而不必让你每次重新登录。它不会授予对其他 Azure DevOps 数据的访问权限。

![列出 PAT 必须授予权限的 Log In to Azure DevOps 对话框](sign-in-with-token.png){ width="560" border-effect="line" }

## 为什么某些标签是“Manage”

Azure DevOps 并没有为 DevOps Lens 执行的每一种读取操作提供更窄的 OAuth 作用域。尤其是权限检查和环境 API 需要 **Security → Manage** 和 **Environment → Read &amp; manage**。DevOps Lens 只将这些授权用于上表所述功能；这并不表示应用请求 Azure DevOps **Full access** 或宽泛的 `user_impersonation` 作用域。

## 修复 PAT 缺少的权限

PAT 权限在创建令牌时固定，插件无法在之后添加。

<procedure title="使用新 PAT 重新认证">
    <step>在 Azure DevOps 中创建新令牌，并选择上表中的所有项目。</step>
    <step>在 <ui-path>Settings | Tools | DevOps Lens</ui-path> 中使用 <b>−</b> 移除旧账户。</step>
    <step>通过 <b>+</b> → <b>Log In with Token…</b> 重新添加，并粘贴新令牌。</step>
</procedure>

对于 Microsoft 登录，只需移除账户并重新登录。当前应用所需的权限集会自动请求，不再有权限层级选择器。

完整登录步骤请参阅 [](Authentication-zh.md)。
