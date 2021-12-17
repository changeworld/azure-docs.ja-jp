---
title: Azure AD ロールをユーザーに割り当てる - Azure Active Directory
description: Azure AD ロールを割り当てることにより、Azure Active Directory のユーザーにアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724817"
---
# <a name="assign-azure-ad-roles-to-users"></a>Azure AD ロールをユーザーに割り当てる

Azure Active Directory (Azure AD) のユーザーにアクセス権を付与するには、Azure AD ロールを割り当てます。 ロールは、アクセス許可のコレクションです。 この記事では、Azure portal と PowerShell を使用して Azure AD ロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- 特権ロール管理者または全体管理者
- Azure AD Premium P2 ライセンス (Privileged Identity Management (PIM) を使用する場合)
- PowerShell を使用する場合の AzureADPreview モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

Azure portal を使用して Azure AD ロールを割り当てるには、これらの手順に従います。 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) が有効になっているかどうかによって、エクスペリエンスは異なります。

### <a name="assign-a-role"></a>ロールの割り当て

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** の順に選択して、利用可能なすべてのロールの一覧を表示します。

    ![Azure Active Directory の [ロールと管理者] ページ。](./media/manage-roles-portal/roles-and-administrators.png)

1. ロールを選択して、その割り当てを表示します。

    必要なロールをすばやく見つけるには、 **[フィルターの追加]** を使用してロールをフィルター処理します。

1. **[割り当ての追加]** を選択し、このロールに割り当てるユーザーを選択します。

    次の図とは異なる内容が表示される場合は、PIM が有効になっている可能性があります。 次のセクションを参照してください。

    ![選択したロールの [割り当ての追加] ペイン。](./media/manage-roles-portal/add-assignments.png)

1. **[追加]** を選択してロールを割り当てます。

### <a name="assign-a-role-using-pim"></a>PIM を使用してロールを割り当てる

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) が有効になっている場合は、追加のロールの割り当て機能が提供されます。 たとえば、ユーザーをロールの対象にしたり、期間を設定したりできます。 PIM が有効な場合、Azure portal を使用してロールを割り当てる方法は 2 つあります。 [ロールと管理者] ページまたは PIM エクスペリエンスを使用できます。 どちらの方法でも同じ PIM サービスが使用されます。

[[ロールと管理者]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) ページを使用してロールを割り当てるには、これらの手順に従います。 [[Privileged Identity Management]](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) ページを使用してロールを割り当てる場合は、「[Privileged Identity Management で Azure AD ロールを割り当てる](../privileged-identity-management/pim-how-to-add-role-to-user.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** の順に選択して、利用可能なすべてのロールの一覧を表示します。

    ![PIM が有効な場合の Azure Active Directory の [ロールと管理者] ページ。](./media/manage-roles-portal/roles-and-administrators.png)

1. ロールを選択して、ロールの割り当ての状態 (対象、アクティブ、期限切れ) を確認します。

    必要なロールをすばやく見つけるには、 **[フィルターの追加]** を使用してロールをフィルター処理します。

1. **[割り当ての追加]** を選択します。

1. **[メンバーが選択されていない]** を選択し、このロールに割り当てるユーザーを選択します。

    ![PIM が有効な場合の [割り当ての追加] ページと [メンバーの選択] ペイン。](./media/manage-roles-portal/add-assignments-pim.png)

1. **[次へ]** を選択します。

1. **[設定]** タブで、このロールの割り当てを **[対象]** にするか **[アクティブ]** にするかを選択します。

    "対象" のロールの割り当ては、ユーザーがロールを使用するために 1 つ以上のアクションを実行する必要があることを意味します。 "アクティブ" なロールの割り当ては、ユーザーがロールを使用するためにアクションを実行する必要がないことを意味します。 これらの設定の意味の詳細については、[PIM の用語](../privileged-identity-management/pim-configure.md#terminology)に関するセクションを参照してください。

    ![PIM が有効な場合の [割り当ての追加] ページと [設定] タブ。](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. 残りのオプションを使用して、割り当ての期間を設定します。

1. **[割り当てる]** を選択してロールを割り当てます。

## <a name="powershell"></a>PowerShell

PowerShell を使用して Azure AD ロールを割り当てるには、これらの手順に従います。

### <a name="setup"></a>セットアップ

1. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell ウィンドウで [Connect-AzureAD](/powershell/module/azuread/connect-azuread) を使用して、テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) を使用して、ロールを割り当てるユーザーを取得します。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>ロールの割り当て

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、割り当てるロールを取得します。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) を使用して、ロールを割り当てます。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>PIM を使用してロールを "対象" として割り当てる

PIM が有効な場合は、ユーザーをロールの割り当ての対象にする機能や、ロールの割り当ての開始時刻と終了時刻を定義する機能などの追加機能が提供されます。 これらの機能では、PowerShell コマンドの異なるセットを使用します。 PowerShell と PIM の使用の詳細については、「[Privileged Identity Management の Azure AD ロールのための PowerShell](../privileged-identity-management/powershell-for-azure-ad-roles.md)」を参照してください。


1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、割り当てるロールを取得します。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource) を使用して、特権付きリソースを取得します。 この場合は、テナントです。

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. [New-Object](/powershell/module/microsoft.powershell.utility/new-object) を使用して、新しい `AzureADMSPrivilegedSchedule` オブジェクトを作成し、ロールの割り当ての開始時刻と終了時刻を定義します。

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest) を使用して、ロールを "対象" として割り当てます。

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)の Microsoft Graph API を使用してロールを割り当てるには、これらの手順に従います。

### <a name="assign-a-role"></a>ロールの割り当て

この例では、objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` のセキュリティ プリンシパルに、テナント スコープで課金管理者ロール (ロール定義 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`) が割り当てられます。 すべての組み込みロールの不変のロール テンプレート ID の一覧については、「[Azure AD の組み込みロール](permissions-reference.md)」を参照してください。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。
2. ドロップダウンから HTTP メソッドとして **[POST]** を選択します。 
3. API バージョンとして **[ベータ]** を選択します。
4. [roleAssignments](/graph/api/rbacapplication-post-roleassignments) API を使用してロールを割り当てます。 次の詳細を URL と要求本文に追加し、 **[クエリの実行]** を選択します。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>PIM を使用してロールを割り当てる

この例では、objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` のセキュリティ プリンシパルに、課金管理者 (ロール定義 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`) に対する 180 日間の期限付きの対象のロールの割り当てが割り当てられています。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。
2. ドロップダウンから HTTP メソッドとして **[POST]** を選択します。 
3. API バージョンとして **[ベータ]** を選択します。
4. 次の詳細を URL と要求本文に追加し、 **[クエリの実行]** を選択します。

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

次の例では、セキュリティ プリンシパルに、課金管理者に対する永続的な対象のロールの割り当てが割り当てられています。

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

ロールの割り当てをアクティブにするには、次の API を使用します。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>次のステップ

- [Azure AD ロールの割り当てを一覧表示する](view-assignments.md)
- [PowerShell を使用してリソース スコープのカスタム ロールを割り当てる](custom-assign-powershell.md)
- [Azure AD の組み込みロール](permissions-reference.md)
