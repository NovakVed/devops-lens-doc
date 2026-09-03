# 権限

<tldr>
    <p><b>Microsoft サインイン:</b> Full/Standard の選択はありません。%product% はブラウザーで Microsoft を開き、監査済みのアプリ必須権限セットを 1 つだけ要求します。</p>
    <p><b>個人用アクセス トークン:</b> 同じ機能を手動で選択します。<b>Full access</b> を選ぶ必要はありません。</p>
</tldr>

%product% が要求するのは、アプリ内の機能が実際に使用する権限だけです。このセットは、プルリクエストのレビュー、作業項目のリンク、パイプライン、承認、テスト、環境、エージェントなど DevOps Lens の全機能を利用できるように設計されていますが、Azure DevOps アカウントへの無制限アクセスではありません。

Microsoft の通常の同意ページは引き続き表示されます。承認前に要求内容を確認する正式な場所はこのページです。DevOps Lens は、その前に独自の権限レベルダイアログを表示しなくなりました。

> Azure DevOps のスコープは、より狭いスコープを継承します。たとえば `vso.code_write` には `vso.code` が、`vso.test` には `vso.profile` が含まれます。DevOps Lens は継承済みのスコープを重複して要求しません。Microsoft の [Azure DevOps OAuth スコープ一覧](https://learn.microsoft.com/ja-jp/azure/devops/integrate/get-started/authentication/oauth?view=azure-devops#scopes)も参照してください。
> {style="note"}

## アプリに必要な権限

OAuth では下記のスコープコードを自動的に要求します。PAT を作成する場合は、Azure DevOps のトークン作成画面で対応する項目を選択してください。

| PAT の設定                                  | OAuth スコープ                      | DevOps Lens が使用する理由 |
|---------------------------------------------|-------------------------------------|----------------------------|
| **Code → Read &amp; write + Status**        | `vso.code_write`, `vso.code_status` | Git/PR データの読み取り、コメント、投票、完了、PR/ブランチ/ラベルの更新、PR/コミットのチェックの読み取り。 |
| **Identity → Read**                         | `vso.identity`                      | @メンション用のユーザー検索と解決。 |
| **Work Items → Read &amp; write**           | `vso.work_write`                    | 関連作業項目の読み取りとリンク/リンク解除、PR 完了時の任意の状態更新。 |
| **Project and Team → Read**                 | `vso.project`                       | 既定の **Mine** PR ビューでチームに割り当てられた項目を表示するためのチーム情報。 |
| **Security → Manage**                       | `vso.security_manage`               | 自分のブランチポリシーのバイパス権限とパイプライン権限の確認。この確認用の読み取り専用スコープは Azure にありません。 |
| **Build → Read &amp; execute**              | `vso.build_execute`                 | 実行、ログ、成果物、定義、カバレッジの読み取り、実行の開始/キャンセル/再試行、定義と保持リースの編集。 |
| **Pipeline Resources → Use**                | `vso.pipelineresources_use`         | 保護されたパイプラインリソースの使用要求の承認または拒否。 |
| **Test Management → Read**                  | `vso.test`                          | テスト実行、結果、カバレッジの読み取り。アバターに使用するプロフィールアクセスも含まれます。 |
| **Environment → Read &amp; manage**         | `vso.environment_manage`            | 環境とデプロイ記録の読み取り。Azure には読み取り専用の環境スコープがなく、Agents ビューで使うエージェントプールアクセスも含まれます。 |

OAuth はさらに Microsoft に `offline_access` を要求します。これにより IDE を再起動するたびにサインインし直さずにセッションを更新できますが、追加の Azure DevOps データへのアクセスは許可されません。

![PAT に付与すべき権限を一覧表示する Log In to Azure DevOps ダイアログ](sign-in-with-token-ja.png){ width="560" border-effect="line" }

## 一部のラベルが「Manage」である理由

DevOps Lens が行うすべての読み取り操作に、より狭い OAuth スコープが用意されているわけではありません。特に権限チェックと環境 API には **Security → Manage** と **Environment → Read &amp; manage** が必要です。DevOps Lens は上記の機能のためだけにこれらを使用します。Azure DevOps の **Full access** や広範な `user_impersonation` スコープを要求するという意味ではありません。

## PAT の不足権限を修正する

PAT の権限は作成時に固定され、後からプラグインで追加することはできません。

<procedure title="新しい PAT で再認証する">
    <step>Azure DevOps で、上の表のすべての項目を選択した新しいトークンを作成します。</step>
    <step><ui-path>Settings | Tools | DevOps Lens</ui-path> で <b>−</b> を使って古いアカウントを削除します。</step>
    <step><b>+</b> → <b>Log In with Token…</b> で追加し直し、新しいトークンを貼り付けます。</step>
</procedure>

Microsoft サインインの場合は、アカウントを削除してもう一度サインインするだけです。現在のアプリ必須セットが自動的に要求され、権限レベルの選択画面はありません。

詳しいサインイン手順は [](Authentication-ja.md) を参照してください。
