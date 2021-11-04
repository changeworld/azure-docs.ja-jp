---
title: アプリ登録の作成への制限を削除する - Azure AD | Microsoft Docs
description: Azure AD Active Directory に無制限のアプリの登録を許可するカスタム ロールを割り当てる
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdffe02ad1f0b5bc0b1881781f35a056d9a6a2ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057155"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>クイック スタート:無制限のアプリの登録を作成するアクセス許可を付与する

このクイック スタート ガイドでは、無制限の数のアプリの登録を作成するアクセス許可があるカスタム ロールを作成した後、そのロールをユーザーに割り当てます。 その後、割り当てられたユーザーは、Azure portal、Azure AD PowerShell、または Microsoft Graph API を使用して、アプリケーションの登録を作成できます。 組み込みのアプリケーション開発者ロールとは異なり、このカスタム ロールには、無制限の数のアプリの登録を作成する権限が付与されます。 アプリケーション開発者ロールにもこの権限は付与されますが、[ディレクトリ全体のオブジェクトのクォータ](../enterprise-users/directory-service-limits-restrictions.md)に達することがないように、作成されるオブジェクトの合計数は 250 に制限されています。 Azure AD カスタム ロールの作成と割り当てに必要な最小限の特権を持つロールは、特権ロール管理者です。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者またはグローバル管理者
- PowerShell を使用する場合の AzureADPreview モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、「[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)」をご覧ください。

## <a name="azure-portal"></a>Azure portal

### <a name="create-a-custom-role"></a>カスタム ロールを作成する

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択してから、 **[新しいカスタム ロール]** を選択します。

    ![[ロールと管理者] ページでロールを作成または編集する](./media/quickstart-app-registration-limits/new-custom-role.png)

1. **[基本]** タブで、ロールの [名前] として「Application Registration Creator」(アプリケーション登録作成者) を指定し、[ロールの説明] に「Can create an unlimited number of application registrations」(無制限の数のアプリケーションの登録を作成できる) と入力して、**[次へ]** を選択します。

    ![[基本] タブでカスタム ロールの名前と説明を指定する](./media/quickstart-app-registration-limits/basics-tab.png)

1. **[アクセス許可]** タブで、検索ボックスに「microsoft.directory/applications/create」と入力し、必要なアクセス許可の横にあるチェックボックスをオンにした後、**[次へ]** を選択します。

    ![[アクセス許可] タブでカスタム ロールのアクセス許可を選択する](./media/quickstart-app-registration-limits/permissions-tab.png)

1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

### <a name="assign-the-role"></a>ロールを割り当てる

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択します。

1. [アプリケーション登録作成者] ロールを選択し、**[割り当ての追加]** を選択します。

1. 目的のユーザーを選択し、**[選択]** をクリックして、ユーザーをロールに追加します。

これで完了です。 このクイックスタートでは、無制限の数のアプリの登録を作成するアクセス許可を持つカスタム ロールを作成した後、そのロールをユーザーに割り当てることに成功しました。

> [!TIP]
> Azure portal を使用してアプリケーションにロールを割り当てるには、[割り当て] ページの [検索] ボックスにアプリケーションの名前を入力します。 既定では、アプリケーションは一覧に表示されませんが、検索結果には返されます。

### <a name="app-registration-permissions"></a>アプリの登録とアクセス許可

アプリケーションの登録を作成する権限を付与するために使用できるアクセス許可は 2 つあり、それぞれ動作が異なります。

- microsoft.directory/applications/createAsOwner:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者として追加され、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になります。
- microsoft.directory/applications/create:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者としては追加されず、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になりません。 担当者がディレクトリ レベルのクォータに達するまでアプリの登録を作成できないようにするものはないため、このアクセス許可は慎重に使用してください。 両方のアクセス許可が割り当てられている場合は、このアクセス許可が優先されます。

## <a name="powershell"></a>PowerShell

### <a name="create-a-custom-role"></a>カスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role"></a>ロールを割り当てる

次の PowerShell スクリプトを使用してロールを割り当てます。

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-custom-role"></a>カスタム ロールを作成する

カスタム ロールを作成するための HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role"></a>ロールを割り当てる

ロールの割り当てでは、セキュリティ プリンシパル ID (ユーザーでもサービス プリンシパルでも可)、ロール定義 (ロール) ID、および Azure AD リソース スコープを組み合わせます。

カスタム ロールを割り当てるための HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>次のステップ

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)でご意見をお寄せください。
- Azure AD ロールの詳細については、「[Azure の組み込みロール](permissions-reference.md)」を参照してください。
- 既定のユーザー アクセス許可について詳しくは、[ゲスト ユーザーとメンバー ユーザーの既定のアクセス許可の比較](../fundamentals/users-default-permissions.md)に関するページを参照してください。
