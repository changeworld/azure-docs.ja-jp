---
title: Azure AD ロールベースのアクセス制御でカスタム ロールを作成する | Microsoft Docs
description: Azure Active Directory リソースにリソース スコープを使用してカスタム Azure AD ロールを作成し、割り当てます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 10/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8425c85f74e5540094be4dab12eb3e3e7c931d1b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057326"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Azure Active Directory でカスタム ロールを作成して割り当てる

この記事では、Azure Active Directory (Azure AD) で新しいカスタム ロールを作成する方法について説明します。 カスタム ロールの基本については、[カスタム ロールの概要](custom-overview.md)を参照してください。 ロールを割り当てることができるのは、ディレクトリ レベルのスコープまたはアプリ登録リソースのスコープだけです。

カスタム ロールは、Azure AD [概要] ページの [[ロールと管理者]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) タブで作成できます。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者またはグローバル管理者
- PowerShell を使用する場合の AzureADPreview モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="create-a-role-in-the-azure-portal"></a>Azure portal でロールを作成する

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>新しいカスタム ロールを作成してアプリ登録を管理するためのアクセス権を付与する

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]**  >  **[新しいカスタム ロール]** を選択します。

   ![[ロールと管理者] ページでロールを作成または編集する](./media/custom-create/new-custom-role.png)

1. **[基本]** タブにロールの名前と説明を指定し、 **[次へ]** をクリックします。

   ![[基本] タブでカスタム ロールの名前と説明を指定する](./media/custom-create/basics-tab.png)

1. **[アクセス許可]** タブで、アプリ登録の基本的なプロパティと資格情報のプロパティを管理するために必要な権限を選択します。 各アクセス許可について詳しくは、「[Azure Active Directory でのアプリケーション登録のサブタイプとアクセス許可](custom-available-permissions.md)」をご覧ください。
   1. 最初に、検索バーに「credentials」と入力し、`microsoft.directory/applications/credentials/update` アクセス許可を選択します。

      ![[アクセス許可] タブでカスタム ロールのアクセス許可を選択する](./media/custom-create/permissions-tab.png)

   1. 次に、検索バーに「basic」と入力し、`microsoft.directory/applications/basic/update` アクセス許可を選択し、 **[次へ]** をクリックします。
1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

カスタム ロールが、割り当て可能なロールの一覧に表示されます。

## <a name="create-a-role-using-powershell"></a>PowerShell を使用してロールを作成する

### <a name="connect-to-azure"></a>Azure に接続する

Azure Active Directory に接続するには、次のコマンドレットを使用します。

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>カスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-powershell"></a>PowerShell を使用してカスタム ロールを割り当てる

次の PowerShell スクリプトを使用して、ロールを割り当てます。

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-the-microsoft-graph-api"></a>Microsoft Graph API を使用してロールを作成する

1. ロール定義を作成します。

    カスタム ロール定義を作成するための HTTP 要求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"` は、要件に応じて本文で送信される省略可能なパラメーターです。 共通パラメーターを使用して複数の異なるカスタム ロールを作成する必要がある場合、テンプレートを作成し、`templateId` 値を定義することをお勧めします。 PowerShell コマンドレット `(New-Guid).Guid`を使用し、`templateId` 値を事前に生成できます。 

1. ロールの割り当てを作成します。

    カスタム ロール定義を作成するための HTTP 要求。

    POST

    ```http
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ```http
    {
        "principalId":"<GUID OF USER>",
        "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
        "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
    }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>カスタム ロールをスコープ指定してリソースに割り当てる

組み込みロールと同様に、既定では、組織内のすべてのアプリ登録にアクセス許可を付与するために、カスタム ロールは既定の組織全体のスコープで割り当てられます。 さらに、カスタム ロールおよびいくつかの関連する組み込みロール (Azure AD リソースの種類によって異なります) は、単一の Azure AD リソースのスコープで割り当てることもできます。 これにより、2 つ目のカスタム ロールを作成せずに、1 つのアプリの資格情報と基本プロパティを更新できるアクセス許可をユーザーに付与できます。

1. アプリケーション開発者のアクセス許可を使用して、[Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。

1. 付与しようとするアクセス権によって管理されるアプリ登録を選択します。 Azure AD 組織内のアプリ登録の完全な一覧を表示するには、 **[すべてのアプリケーション]** を選択する必要がある場合があります。

    ![ロールの割り当てのリソース スコープとしてアプリ登録を選択する](./media/custom-create/appreg-all-apps.png)

1. [アプリの登録] で、 **[ロールと管理者]** を選択します。 まだ 1 つも作成していない場合は、[前の手順](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)の指示を参照してください。

1. ロールを選択して、 **[割り当て]** ページを開きます。

1. **[割り当ての追加]** を選択してユーザーを追加します。 このユーザーには、選択したアプリ登録に対してのみアクセス許可が付与されます。

## <a name="next-steps"></a>次のステップ

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)でご意見をお寄せください。
- ロールのアクセス許可の詳細については、「[Azure AD の組み込みロール](permissions-reference.md)」を参照してください。
- 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context)を参照してください。
