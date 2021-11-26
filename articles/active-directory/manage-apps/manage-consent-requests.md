---
title: アプリケーションの同意の管理と同意要求の評価
description: ユーザーの同意が無効または制限されている場合に同意要求を管理する方法、および Azure Active Directory でアプリケーションに対するテナント全体の管理者の同意要求を評価する方法について説明します。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: e871bfe8f6fa787a3ffffc4fa0efb2df6d920a0e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548389"
---
# <a name="manage-consent-to-applications-and-evaluate-consent-requests"></a>アプリケーションの同意の管理と同意要求の評価

Microsoft では、ユーザーが検証された発行元からのアプリと選択したアクセス許可に対してのみ同意できるように、[ユーザーの同意を制限する](../../active-directory/manage-apps/configure-user-consent.md)ことをお勧めします。 このポリシーを満たしていないアプリの場合、組織のセキュリティ チームと ID 管理者チームに意思決定プロセスが一元化されます。

エンドユーザーの同意が無効または制限された後でも、ビジネス クリティカルなアプリケーションの使用を許可しながら、組織のセキュリティを確保するための重要な考慮事項がいくつかあります。 これらの手順は、サードパーティ製のアプリケーションにおける管理されていないアカウントの使用を制限しながら、組織のサポート チームと IT 管理者への影響を最小限に抑えるために不可欠です。

## <a name="process-changes-and-education"></a>変更と教育を処理する

 1. [管理者の同意ワークフロー](configure-admin-consent-workflow.md)を有効にして、ユーザーが同意画面から直接管理者の承認を要求できるようにすることを検討してください。

 2. すべての管理者が[アクセス許可と同意フレームワーク](../develop/consent-framework.md)、[同意プロンプト](../develop/application-consent-experience.md)のしくみ、および[テナント全体の管理者の同意要求を評価する](#evaluating-a-request-for-tenant-wide-admin-consent)方法を理解していることを確認します。
 3. ユーザーがアプリケーションの管理者の承認を要求する方法について、組織の既存のプロセスを確認し、必要に応じて更新します。 プロセスが変更された場合:
    * 関連するドキュメント、監視、自動化などを更新します。
    * 影響を受けるすべてのユーザー、開発者、サポート チーム、および IT 管理者にプロセスの変更を伝達します。

## <a name="auditing-and-monitoring"></a>監査と監視

1. 組織の[アプリと許可されたアクセス許可を監査](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions)し、認められていない、または疑わしいアプリケーションが以前にデータへのアクセスを許可されていないことを確認します。

2. OAuth 同意を要求する疑わしいアプリケーションに対する追加のベスト プラクティスと保護手段については、[Office 365 での不正な同意の許可の検出と修復](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)に関するページを確認します。

3. 組織が適切なライセンスを持っている場合:

    * [Microsoft Defender for Cloud Apps で追加の OAuth アプリケーション監査機能](/cloud-app-security/investigate-risky-oauth)を使用します。
    * [Azure Monitor Workbooks を使用して、アクセス許可および同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)に関連するアクティビティを監視します。 *Consent Insights* ワークブックには、失敗した同意要求の数を示すアプリの一覧が表示されます。 これは、管理者の同意を許可するかどうかを管理者が確認および判断するため、アプリケーションの優先順位を決定するのに役立ちます。

### <a name="additional-considerations-for-reducing-friction"></a>摩擦の軽減に関するその他の考慮事項

既に使用されている、信頼されたビジネス クリティカルなアプリケーションへの影響を最小限に抑えるには、許可するユーザーの同意が多いアプリケーションに管理者の同意を事前に許可することを検討してください。

1. サインイン ログまたは同意許可アクティビティに基づいて、使用率が高い組織に既に追加されているアプリのインベントリを取得します。 PowerShell [スクリプト](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)を使用すると、多数のユーザーの同意が許可されたアプリケーションをすばやく簡単に検出できます。

2. 管理者の同意がまだ許可されていない上位のアプリケーションを評価します。

   > [!IMPORTANT]
   > アプリケーションは、組織内の多くのユーザーが既に各自で同意している場合でも、テナント全体の管理者の同意を許可する前に慎重に評価してください。

3. 承認された各アプリケーションについて、以下に記載されているいずれかの方法を使用して、テナント全体の管理者の同意を許可します。

4. 承認されたアプリケーションごとに、[ユーザー アクセスを制限すること](configure-user-consent.md)を検討してください。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>テナント全体の管理者の同意要求を評価する

テナント全体の管理者の同意を許可する際は、慎重に行う必要があります。  アクセス許可は、組織全体に代わって許可され、高度な権限を必要とする操作を試行するためのアクセス許可が含まれることがあります。 たとえば、ロール管理、すべてのメールボックスまたはすべてのサイトへのフルアクセス、完全なユーザー偽装などです。

テナント全体の管理者の同意を許可する前に、許可するアクセス レベルに対して、アプリケーションとアプリケーションの発行元を信頼していることを確認する必要があります。 アプリケーションを制御しているユーザーと、アプリケーションがなぜアクセス許可を要求しているのか確信を持てない場合は、*同意を許可しないでください*。

次の一覧に、管理者の同意を求める要求を評価する際に考慮すべき推奨事項を示します。

* **Microsoft ID プラットフォームでの [アクセス許可と同意フレームワーク](../develop/consent-framework.md)について理解する。**

* **[委任されたアクセス許可とアプリケーションのアクセス許可の違い](../develop/v2-permissions-and-consent.md#permission-types)について理解する。**

   アプリケーションのアクセス許可を許可すると、アプリケーションはユーザーの介入を必要とせずに組織全体のデータにアクセスできます。 委任されたアクセス許可を使用すると、ある時点でアプリケーションにサインインしたユーザーの代わりにアプリケーションを操作できます。

* **要求されているアクセス許可を理解します。**

   アプリケーションによって要求されたアクセス許可は、[同意プロンプト](../develop/application-consent-experience.md)に一覧表示されます。 アクセス許可のタイトルを展開すると、アクセス許可の説明が表示されます。 アプリケーションのアクセス許可の説明は、通常 "サインインしたユーザーなし" で終わります。 委任されたアクセス許可の説明は、通常 "サインインしたユーザーの代わりに" で終わります。 Microsoft Graph API のアクセス許可については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」で説明されています。他の API で公開されるアクセス許可については、ドキュメントを参照してください。

   要求されているアクセス許可を把握していない場合は、*同意を許可しないでください*。

* **アクセス許可を要求しているアプリケーションとアプリケーションの発行元を把握します。**

   他のアプリケーションに偽装している悪意のあるアプリケーションに注意してください。

   アプリケーションまたはその発行元の正当性が不明な場合は、*同意を許可しないでください*。 代わりに、追加の確認 (たとえば、アプリケーションの発行元から直接) を取ります。

* **要求されたアクセス許可が、アプリケーションに期待される機能と一致していることを確認します。**

   たとえば、SharePoint サイト管理を提供するアプリケーションでは、すべてのサイト コレクションの読み取りに委任アクセスを求める場合がありますが、すべてのメールボックスへのフルアクセス、またはディレクトリ内の完全な借用権限は必要ありません。

   アプリケーションが必要以上のアクセス許可を要求していると思われる場合は、*同意を許可しないでください*。 詳細については、アプリケーションの発行元にお問い合わせください。

## <a name="granting-consent-as-an-administrator"></a>管理者として同意を許可する

### <a name="granting-tenant-wide-admin-consent"></a>テナント全体の管理者の同意を許可する

テナント全体の管理者の同意を Azure portal、Azure AD PowerShell、または同意プロンプト自体から許可する手順については、「[アプリケーションに対してテナント全体の管理者の同意を許可する](grant-admin-consent.md)」を参照してください。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>特定のユーザーに代わって同意を許可する

管理者は、組織全体に同意を許可するのではなく、[Microsoft Graph API](/graph/use-the-api) を使用して、1 人のユーザーに代わって委任されたアクセス許可に同意を許可することもできます。 Microsoft Graph PowerShell を使用した詳細な例については、[「PowerShell を使用した1 人のユーザーに代わって同意を許可する」](#grant-consent-on-behalf-of-a-single-user-using-powershell)を参照してください。

## <a name="limiting-user-access-to-applications"></a>アプリケーションへのユーザー アクセスを制限する

テナント全体の管理者の同意が許可されている場合でも、ユーザーのアプリケーションへのアクセスは制限される可能性があります。 アプリケーションへのユーザー割り当てを要求する方法の詳細については、[ユーザーとグループを割り当てる方法](./assign-user-or-group-access-portal.md)に関する記事を参照してください。

その他の複雑なシナリオの処理方法などについての詳細な概要については、[Azure AD を使用したアプリケーション アクセス管理](what-is-access-management.md)に関する記事を参照してください。

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>すべてのアプリケーションに対して今後のユーザーの同意操作をすべて無効にする

ディレクトリ全体に対するユーザーの同意を無効にすると、エンド ユーザーは、どのアプリケーションにも同意できなくなります。 管理者は、依然としてユーザーに代わって同意できます。 アプリケーションの同意と、同意する理由または同意しない理由の詳細については、「[ユーザーおよび管理者の同意について](../develop/howto-convert-app-to-be-multi-tenant.md)」をご覧ください。

ディレクトリ全体で今後のユーザーの同意動作をすべて無効にするには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者** としてサインインします。
2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。
3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** 項目を選択します。
4. **[エンタープライズ アプリケーション]** を選択し、 **[管理]** セクションの **[ユーザー設定]** を選択します。
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="すべてのアプリに対するユーザーの同意操作の無効化。":::
5. **[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** トグルを **[いいえ]** に設定することによって将来のすべてのユーザーの同意操作を無効にし、 **[保存]** ボタンをクリックします。

## <a name="grant-consent-on-behalf-of-a-single-user-using-powershell"></a>PowerShell を使用した 1 人のユーザーに代わって同意を許可する

ユーザーが自分自身に代わって同意を許可する場合、次のことが起こる：

1. クライアント アプリケーションのサービス プリンシパルが作成されます (存在しない場合)。 サービス プリンシパルは、Azure ADテナント内にアプリケーションまたはサービスのインスタンスです。 アプリまたはサービスに許可されたアクセス権は、このサービス プリンシパル オブジェクトに関連付けられている。
1. アプリケーションがアクセスを必要とする API ごとに、その API に対して、アプリケーションがユーザーに代わってアクセスするために必要なアクセス許可に対して委任されたアクセス許可が作成されます。 委任されたアクセス許可付与は、ユーザーがサインインした場合に、アプリケーションがユーザーに代わって API にアクセスする権限を許可します。
1. ユーザーはクライアント アプリケーションを割り当てられます。 アプリケーションをユーザーに割り当てると、そのユーザーの [マイ アプリ](my-apps-deployment-plan.md) ポータルにアプリケーションがリストに確実に表示され、ユーザーに代わって許可されたアクセス権の確認と取り消しが可能になります。

1 人のユーザーに代わってアプリケーションに同意を許可するステップを手動で実行するには、以下の詳細が必要です。

* 同意を許可するアプリのアプリ ID (これを "クライアント アプリケーション" と呼ぶ)。
* クライアント アプリケーションに必要な API アクセス許可。 API のアプリ ID とアクセス許可 ID または要求値を知っている必要があります。
* アクセス権が許可されるユーザーのユーザー名またはオブジェクト ID。

次の例では、[Microsoft Graph PowerShell](/graph/powershell/get-started)を使用して、上記の 3 つのステップを実行して、1 人のユーザーに代わって同意を許可します。 この例では、クライアント アプリケーションは[Microsoft Graph Explorer](https://aka.ms/ge)であり、Microsoft Graph API へのアクセスを許可します。

```powershell
# The app for which consent is being granted. In this example, we're granting access
# to Microsoft Graph Explorer, an application published by Microsoft.
$clientAppId = "de8bc8b5-d9f9-48b1-a8ad-b748da725064" # Microsoft Graph Explorer

# The API to which access will be granted. Microsoft Graph Explorer makes API 
# requests to the Microsoft Graph API, so we'll use that here.
$resourceAppId = "00000003-0000-0000-c000-000000000000" # Microsoft Graph API

# The permissions to grant. Here we're including "openid", "profile", "User.Read"
# and "offline_access" (for basic sign-in), as well as "User.ReadBasic.All" (for 
# reading other users' basic profile).
$permissions = @("openid", "profile", "offline_access", "User.Read", "User.ReadBasic.All")

# The user on behalf of who access will be granted. The app will be able to access 
# the API on behalf of this user.
$userUpnOrId = "user@example.com"

# Step 0. Connect to Microsoft Graph PowerShell. We need User.ReadBasic.All to get
#    users' IDs, Application.ReadWrite.All to list and create service principals, 
#    DelegatedPermissionGrant.ReadWrite.All to create delegated permission grants, 
#    and AppRoleAssignment.ReadWrite.All to assign an app role.
#    WARNING: These are high-privilege permissions!
Connect-MgGraph -Scopes ("User.ReadBasic.All Application.ReadWrite.All " `
                        + "DelegatedPermissionGrant.ReadWrite.All " `
                        + "AppRoleAssignment.ReadWrite.All")

# Step 1. Check if a service principal exists for the client application. 
#     If one does not exist, create it.
$clientSp = Get-MgServicePrincipal -Filter "appId eq '$($clientAppId)'"
if (-not $clientSp) {
   $clientSp = New-MgServicePrincipal -AppId $clientAppId
}

# Step 2. Create a delegated permission grant granting the client app access to the
#     API, on behalf of the user. (This example assumes that an existing delegated 
#     permission grant does not already exist, in which case it would be necessary 
#     to update the existing grant, rather than create a new one.)
$user = Get-MgUser -UserId $userUpnOrId
$resourceSp = Get-MgServicePrincipal -Filter "appId eq '$($resourceAppId)'"
$scopeToGrant = $permissions -join " "
$grant = New-MgOauth2PermissionGrant -ResourceId $resourceSp.Id `
                                     -Scope $scopeToGrant `
                                     -ClientId $clientSp.Id `
                                     -ConsentType "Principal" `
                                     -PrincipalId $user.Id

# Step 3. Assign the app to the user. This ensure the user can sign in if assignment
#     is required, and ensures the app shows up under the user's My Apps.
if ($clientSp.AppRoles | ? { $_.AllowedMemberTypes -contains "User" }) {
    Write-Warning ("A default app role assignment cannot be created because the " `
                 + "client application exposes user-assignable app roles. You must " `
                 + "assign the user a specific app role for the app to be listed " `
                 + "in the user's My Apps access panel.")
} else {
    # The app role ID 00000000-0000-0000-0000-000000000000 is the default app role
    # indicating that the app is assigned to the user, but not for any specific 
    # app role.
    $assignment = New-MgServicePrincipalAppRoleAssignedTo `
          -ServicePrincipalId $clientSp.Id `
          -ResourceId $clientSp.Id `
          -PrincipalId $user.Id `
          -AppRoleId "00000000-0000-0000-0000-000000000000"
}
```

## <a name="next-steps"></a>次のステップ

* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
* [Azure Active Directory でエンド ユーザーがアプリケーションに同意する方法を構成する](configure-user-consent.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)
