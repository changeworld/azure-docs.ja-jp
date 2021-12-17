---
title: ユーザーの Azure AD ロールの割り当てを一覧表示する - Azure Active Directory
description: ユーザーの Azure AD ロールの割り当てを一覧表示する方法について説明する
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58647d37da29066c14457ae6624763c800e6f123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776420"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>ユーザーの Azure AD ロールの割り当てを一覧表示する

ロールは、ユーザーに直接割り当てるか、グループを介して推移的に割り当てることができます。 この記事では、ユーザーへの Azure AD ロールの割り当てを一覧表示する方法について説明します。 グループへのロールの割り当ての詳細については、「[Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- PowerShell を使用する場合の AzureADPreview モジュール
- PowerShell を使用する場合の Microsoft.Graph モジュール
- 管理者の同意 (Microsoft Graph API の Graph エクスプローラーを使用する場合)

詳細については、「[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)」をご覧ください。

## <a name="azure-portal"></a>Azure portal
ユーザーの Azure AD ロールを Azure portal を使用して一覧表示するには、これらの手順に従ってください。 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) が有効になっているかどうかによって、エクスペリエンスは異なります。

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

2. **[Azure Active Directory]**  >  **[ユーザー]**  >  *[ユーザー名]*  >  **[割り当てられたロール]** の順に選択します。

    ユーザーに割り当てられたロールの一覧は、さまざまなスコープで表示できます。 さらに、ロールが直接割り当てられているのか、グループを介して割り当てられているのかを確認できます。
    
    ![Azure portal でのユーザーに割り当てられているロールの一覧](./media/list-role-assignments-users/list-role-definition.png)

    Premium P2 ライセンスを持っている場合は、PIM エクスペリエンスが表示されます。これには、ロール割り当ての詳細として、適格、アクティブ、および有効期限切れがあります。

    ![PIM でのユーザーに割り当てられているロールの一覧](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

PowerShell を使用して、ユーザーに割り当てられた Azure AD ロールを一覧表示するには、これらの手順に従います。

1. [Install-module](/powershell/azure/active-directory/install-adv2) を使用して、AzureADPreview と Microsoft.Graph モジュールをインストールします。
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して、AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. PowerShell ウィンドウ内で [Connect-AzureAD](/powershell/module/azuread/connect-azuread) を使用して、自分のテナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```
4. [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) を使用して、ユーザーに直接割り当てられたロールを取得します。

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. ユーザーに割り当てられた推移的なロールを取得するには、次のコマンドレットを使用します。
  
    a. [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup) を使用して、すべてのロール割り当て可能グループの一覧を取得します。  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. [Connect-MgGraph](/graph/powershell/get-started) を使用してサインインし、Microsoft Graph PowerShell コマンドレットを使用します。
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    c. [checkMemberObjects](/graph/api/user-checkmemberobjects) API を使用して、ユーザーがメンバーになっているロール割り当て可能グループを特定します。 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) を使用して、グループを反復処理し、それらに割り当てられたロールを取得します。
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. ユーザーの直接ロール割り当てと推移的ロール割り当ての両方を組み合わせます。
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)で Microsoft Graph API を使用して、ユーザーに割り当てられている Azure AD ロールを一覧表示するには、これらの手順に従います。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。

1. [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API を使用して、ユーザーに直接割り当てられたロールを取得します。 次のクエリを URL に追加し、 **[クエリの実行]** を選択します。

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. ユーザーに割り当てられた推移的なロールを取得するには、これらの手順に従います。

    a. [List groups](/graph/api/group-list) を使用して、すべてのロール割り当て可能グループの一覧を取得します。
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. この一覧を [checkMemberObjects](/graph/api/user-checkmemberobjects) API に渡して、ユーザーがメンバーになっているロール割り当て可能グループを特定します。 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    c. [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API を使用して、グループを反復処理し、それらに割り当てられたロールを取得します。
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>次のステップ

* [Azure AD ロールの割り当てを一覧表示する](view-assignments.md)。
* [Azure AD ロールをユーザーに割り当てる](manage-roles-portal.md)。
* [Azure AD ロールをグループに割り当てる](groups-assign-role.md)
