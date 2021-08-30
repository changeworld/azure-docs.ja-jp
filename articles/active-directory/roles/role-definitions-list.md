---
title: Azure AD ロールの定義を一覧表示する - Azure AD
description: Azure の組み込みロールとカスタム ロールの両方を一覧表示する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748956"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD ロールの定義を一覧表示する

ロールの定義は、読み取り、書き込み、削除などの実行できるアクセス許可のコレクションです。 通常は単に "ロール" と呼ばれます。 Azure Active Directory には、60 を超える組み込みロールがあります。また、独自のカスタム ロールを作成することもできます。 "これらのロールで何ができるか" については、ロールごとのアクセス許可の詳細な一覧を参照してください。

この記事では、Azure AD 組み込みロールとカスタムロールの一覧を、そのアクセス許可と共に一覧表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

- PowerShell を使用する場合の AzureADPreview モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

    ![Azure portal でのロールの一覧](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 右側にある省略記号を選択し、 **[説明]** をクリックすると、ロールのアクセス許可の完全な一覧が表示されます。

    このページには、ロールの管理について説明している関連ドキュメントへのリンクが含まれています。

    ![[全体管理者 - 説明] ページを示すスクリーンショット。](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

PowerShell を使用して Azure AD ロールを一覧表示するには、次の手順を実行します。

1. PowerShell ウィンドウを開き、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して、AzureADPreview モジュールをインポートします。 詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. PowerShell ウィンドウ内で [Connect-AzureAD](/powershell/module/azuread/connect-azuread) を使用して、ご自身のテナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```
3. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) を使用して、すべてのロールを取得します。

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. ロールのアクセス許可の一覧を表示するには、次のコマンドレットを使用します。
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph エクスプローラー](https://aka.ms/ge)内で Microsoft Graph API を使用して、Azure AD ロールを一覧表示するには、次の手順を実行します。

1. [Graph エクスプローラー](https://aka.ms/ge)にサインインします。
2. ドロップダウンから HTTP メソッドとして **[GET]** を選択します。 
3. API バージョンとして **[ベータ]** を選択します。
4. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API を使用するために、次のクエリを追加します。

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. **[Run Query]\(クエリの実行\)** を選択して、ロールを一覧表示します。
6. ロールのアクセス許可を表示するには、次の API を使用します。

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>次のステップ

* [Azure AD ロールの割り当てを一覧表示する](view-assignments.md)。
* [Azure AD ロールをユーザーに割り当てる](manage-roles-portal.md)。
* [Azure AD の組み込みロール](permissions-reference.md)。
