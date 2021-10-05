---
title: Azure AD ロールを異なるスコープで割り当てる - Azure Active Directory
description: Azure Active Directory で、Azure AD ロールを異なるスコープで割り当てる方法について説明します
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 09/13/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 951431ca79bfeb182eb5d1252851edb767120039
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658243"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>Azure AD ロールを異なるスコープで割り当てる

Azure Active Directory (Azure AD) 内で Azure AD ロールを割り当てるときは、通常、テナント全体に適用されるようにします。 ただし、管理単位やアプリケーションの登録など、さまざまなリソースに対して Azure AD ロールを割り当てることもできます。 たとえば、ヘルプデスク管理者ロールを、テナント全体ではなく、特定の管理単位にのみ適用されるよう割り当てることができます。 ロールの割り当てが適用されるリソースは、スコープとも呼ばれます。 この記事では、テナント、管理単位、アプリケーションの登録のスコープで、Azure AD ロールを割り当てる方法について説明します。 スコープの詳細については、[Azure AD での RBAC の概要](custom-overview.md#scope)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- 特権ロール管理者またはグローバル管理者。
- AzureADPreview モジュール (PowerShell を使用する場合)。
- 管理者の同意 (Microsoft Graph API の Graph エクスプローラーを使用する場合)。

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="assign-roles-scoped-to-the-tenant"></a>テナントにスコープ設定されたロールを割り当てる

このセクションでは、テナント スコープでロールを割り当てる方法について説明します。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

    ![Azure Active Directory の [ロールと管理者] ページ。](./media/manage-roles-portal/roles-and-administrators.png)

1. ロールを選択して、その割り当てを表示します。 必要なロールをすばやく見つけるには、 **[フィルターの追加]** を使用してロールをフィルター処理します。

1. **[割り当ての追加]** を選択し、このロールに割り当てるユーザーを選択します。

    ![選択したロールの [割り当ての追加] ペイン。](./media/manage-roles-portal/add-assignments.png)

1. **[追加]** を選択してロールを割り当てます。

### <a name="powershell"></a>PowerShell

PowerShell を使用して Azure AD ロールを割り当てるには、これらの手順に従います。

1. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell ウィンドウで [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 使用し、テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) を使用してユーザーを取得します。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、割り当てるロールを取得します。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. ロールの割り当てのスコープとしてテナントを設定します。

    ```powershell
    $directoryScope = '/'
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) を使用して、ロールを割り当てます。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)の Microsoft Graph API を使用してロールを割り当てるには、これらの手順に従います。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。

1. [List user](/graph/api/user-list) API を使用してユーザーを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API を使用して、割り当てるロールを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API を使用してロールを割り当てます。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>管理単位にスコープされたロールの割り当て

このセクションでは、[管理単位](administrative-units.md)のスコープでロールを割り当てる方法について説明します。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory] > [管理単位]** を選択して、管理単位の一覧を表示します。

1. 管理単位を選択します。

    ![Azure Active Directory の管理単位。](./media/assign-roles-different-scopes/admin-units.png)

1. 左側のナビゲーション メニューから **[ロールと管理者]** を選択して、管理単位で割り当て可能なすべてのロールの一覧を表示します。

    ![Azure Active Directory の [管理単位] の下にある [ロールと管理者] メニュー。](./media/assign-roles-different-scopes/admin-units-roles.png)

1. 目的のロールを選択します。

1. **[割り当ての追加]** を選択してから、このロールに割り当てるユーザーまたはグループを選択します。

1. **[追加]** を選択して、管理単位にスコープ設定したヘルプデスク管理者ロールを割り当てます。

>[!Note] 
>ここには、Azure AD の組み込みロールやカスタム ロールの全一覧は表示されません。 これは予期されることです。 ここには、管理単位でサポートされているオブジェクトに関連するアクセス許可を持つロールが表示されます。 管理単位でサポートされるオブジェクトの一覧については、[こちらのドキュメント](administrative-units.md)を参照してください。

### <a name="powershell"></a>PowerShell

PowerShell を使用して管理単位スコープで Azure AD ロールを割り当てるには、以下の手順を実行します。

1. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell ウィンドウで [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 使用し、テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) を使用してユーザーを取得します。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、割り当てるロールを取得します。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit) を使用して、ロールの割り当てのスコープを設定する管理単位を取得します。

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) を使用して、ロールを割り当てます。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)の Microsoft Graph API を使用して管理単位スコープでロールを割り当てるには、これらの手順に従います。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。

1. [List user](/graph/api/user-list) API を使用してユーザーを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API を使用して、割り当てるロールを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. [List administrativeUnits](/graph/api/administrativeunit-list) API を使用して、ロールの割り当てのスコープを設定する管理単位を取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API を使用してロールを割り当てます。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>ここでは、directhoryScopeId は */foo* ではなく */administrativeUnits/foo* として指定されています。 これは仕様です。 */administrativeUnits/foo* というスコープは、プリンシパルが管理単位のメンバーを (割り当てられたロールに基づいて) 管理できることを意味し、管理単位自体を管理できるわけではありません。 スコープが */foo* の場合は、プリンシパルによって Azure AD オブジェクト自体が管理できることを意味します。 後続のセクションでは、アプリの登録にスコープ設定したロールによってオブジェクト自体を管理するアクセス許可が付与されるので、スコープが */foo* であることがわかります。

## <a name="assign-roles-scoped-to-an-app-registration"></a>アプリの登録にスコープ設定したロールの割り当て

このセクションでは、アプリケーションの登録のスコープでロールを割り当てる方法について説明します。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory] > [アプリの登録]** を選択して、アプリの登録の一覧を表示します。

1. アプリケーションを選択します。 検索ボックスを使用すれば、目的のアプリを探すことができます。

    ![Azure Active Directory のアプリの登録。](./media/assign-roles-different-scopes/app-reg.png)

1. 左側のナビゲーション メニューから **[ロールと管理者]** を選択して、アプリの登録で割り当て可能なすべてのロールの一覧を表示します。

    ![Azure Active Directory のアプリの登録のロール。](./media/assign-roles-different-scopes/app-reg-roles.png)

1. 目的のロールを選択します。

1. **[割り当ての追加]** を選択してから、このロールに割り当てるユーザーまたはグループを選択します。

    ![Azure Active Directory のアプリの登録にスコープ設定したロールの割り当ての追加。](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. **[追加]** を選択して、アプリの登録にスコープ設定したロールを割り当てます。 

    ![Azure Active Directory のアプリの登録にスコープ設定したロール割り当ての追加に成功しました。](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![Azure Active Directory のアプリの登録にスコープ設定したユーザーに割り当てられたロール。](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>ここには、Azure AD の組み込みロールやカスタム ロールの全一覧は表示されません。 これは予期されることです。 アプリの登録の管理に関連するアクセス許可を持つロールのみが表示されます。 


### <a name="powershell"></a>PowerShell

PowerShell を使用してアプリケーションのスコープで Azure AD ロールを割り当てるには、以下の手順を実行します。

1. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell ウィンドウで [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 使用し、テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) を使用してユーザーを取得します。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、割り当てるロールを取得します。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) を使用して、ロールの割り当てのスコープを設定するアプリの登録を取得します。

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) を使用して、ロールを割り当てます。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)の Microsoft Graph API を使用してアプリケーションのスコープでロールを割り当てるには、これらの手順に従います。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。

1. [List user](/graph/api/user-list) API を使用してユーザーを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API を使用して、割り当てるロールを取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. [List applications](/graph/api/application-list) API を使用して、ロールの割り当てのスコープを設定する管理単位を取得します。

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API を使用してロールを割り当てます。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>ここでは、上記のセクションとは異なり、directoryScopeId は */foo* として指定されています。 これは仕様です。 スコープが */foo* の場合は、プリンシパルによって Azure AD オブジェクトが管理できることを意味します。 */administrativeUnits/foo* というスコープは、プリンシパルが管理単位のメンバーを (割り当てられたロールに基づいて) 管理できることを意味し、管理単位自体を管理できるわけではありません。


## <a name="next-steps"></a>次のステップ

* [Azure AD ロールの割り当てを一覧表示する](view-assignments.md)。
* [Azure AD ロールをユーザーに割り当てる](manage-roles-portal.md)。
* [Azure AD ロールをグループに割り当てる](groups-assign-role.md)