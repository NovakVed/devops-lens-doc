# 권한

<tldr>
    <p><b>Microsoft 로그인:</b> Full/Standard 선택이 없습니다. %product%가 브라우저에서 Microsoft를 열고, 검토된 앱 필수 권한 세트 하나만 요청합니다.</p>
    <p><b>개인용 액세스 토큰:</b> 같은 기능을 직접 선택합니다. <b>Full access</b>를 선택할 필요가 없습니다.</p>
</tldr>

%product%는 앱 기능이 실제로 사용하는 권한만 요청합니다. 이 세트는 풀 리퀘스트 리뷰, 작업 항목 연결, 파이프라인, 승인, 테스트, 환경 및 에이전트를 포함한 DevOps Lens의 모든 기능을 사용할 수 있도록 구성되지만, Azure DevOps 계정에 대한 무제한 액세스는 아닙니다.

Microsoft의 일반 동의 페이지는 계속 표시됩니다. 승인하기 전에 요청된 액세스를 검토하는 공식 위치는 이 페이지입니다. DevOps Lens는 그 앞에 별도의 권한 등급 대화 상자를 더 이상 표시하지 않습니다.

> Azure DevOps 범위는 더 좁은 범위를 상속합니다. 예를 들어 `vso.code_write`에는 `vso.code`가, `vso.test`에는 `vso.profile`이 포함됩니다. DevOps Lens는 이미 상속된 범위를 중복 요청하지 않습니다. Microsoft의 [Azure DevOps OAuth 범위 참고 자료](https://learn.microsoft.com/ko-kr/azure/devops/integrate/get-started/authentication/oauth?view=azure-devops#scopes)도 참고하세요.
> {style="note"}

## 앱 필수 권한

OAuth는 아래 범위 코드를 자동으로 요청합니다. PAT를 만들 때는 Azure DevOps 토큰 양식에서 해당 항목을 선택하세요.

| PAT 설정                                    | OAuth 범위                          | DevOps Lens가 사용하는 이유 |
|---------------------------------------------|-------------------------------------|-----------------------------|
| **Code → Read &amp; write + Status**        | `vso.code_write`, `vso.code_status` | Git/PR 데이터 읽기, 댓글, 투표, 완료, PR/브랜치/레이블 업데이트, PR/커밋 검사 읽기. |
| **Identity → Read**                         | `vso.identity`                      | @멘션을 위한 사용자 검색 및 확인. |
| **Work Items → Read &amp; write**           | `vso.work_write`                    | 연결된 작업 항목 읽기와 연결/해제, PR 완료 시 선택적 상태 변경. |
| **Project and Team → Read**                 | `vso.project`                       | 기본 **Mine** PR 보기에서 팀에 할당된 항목을 표시하기 위한 팀 정보. |
| **Security → Manage**                       | `vso.security_manage`               | 자신의 브랜치 정책 우회 권한과 파이프라인 권한 확인. Azure에는 이 확인을 위한 읽기 전용 범위가 없습니다. |
| **Build → Read &amp; execute**              | `vso.build_execute`                 | 실행, 로그, 아티팩트, 정의, 커버리지 읽기, 실행 큐/취소/재시도, 정의와 보존 임대 편집. |
| **Pipeline Resources → Use**                | `vso.pipelineresources_use`         | 보호된 파이프라인 리소스 사용 요청 승인 또는 거부. |
| **Test Management → Read**                  | `vso.test`                          | 테스트 실행, 결과 및 커버리지 읽기. 아바타에 쓰이는 프로필 액세스도 포함됩니다. |
| **Environment → Read &amp; manage**         | `vso.environment_manage`            | 환경과 배포 기록 읽기. Azure에는 읽기 전용 환경 범위가 없으며 Agent pools 보기에 쓰이는 에이전트 풀 액세스도 포함됩니다. |

OAuth는 Microsoft에 `offline_access`도 요청합니다. 이 권한은 IDE를 다시 시작할 때마다 로그인하지 않아도 세션을 갱신하게 해 주며, 추가 Azure DevOps 데이터에 대한 액세스는 부여하지 않습니다.

![PAT에 부여해야 하는 권한을 나열하는 Log In to Azure DevOps 대화 상자](sign-in-with-token-ko.png){ width="560" border-effect="line" }

## 일부 레이블이 “Manage”인 이유

DevOps Lens가 수행하는 모든 읽기 작업에 더 좁은 OAuth 범위가 제공되는 것은 아닙니다. 특히 권한 확인과 환경 API에는 **Security → Manage** 및 **Environment → Read &amp; manage**가 필요합니다. DevOps Lens는 위에 설명한 기능에만 이 권한을 사용합니다. Azure DevOps **Full access** 또는 광범위한 `user_impersonation` 범위를 요청한다는 의미는 아닙니다.

## 누락된 PAT 권한 수정

PAT 권한은 토큰 생성 시 고정되며 플러그인에서 나중에 추가할 수 없습니다.

<procedure title="새 PAT로 다시 인증">
    <step>Azure DevOps에서 위 표의 모든 항목을 선택한 새 토큰을 만듭니다.</step>
    <step><ui-path>Settings | Tools | DevOps Lens</ui-path>에서 <b>−</b>를 사용해 이전 계정을 제거합니다.</step>
    <step><b>+</b> → <b>Log In with Token…</b>으로 다시 추가하고 새 토큰을 붙여 넣습니다.</step>
</procedure>

Microsoft 로그인은 계정을 제거한 뒤 다시 로그인하면 됩니다. 현재 앱 필수 권한 세트가 자동으로 요청되며 권한 등급 선택 화면은 없습니다.

전체 로그인 절차는 [](Authentication-ko.md)을 참고하세요.
