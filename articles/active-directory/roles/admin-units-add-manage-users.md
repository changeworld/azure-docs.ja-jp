---
title: 管理単位のユーザーを追加、削除、一覧表示する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位のユーザーとそのロールのアクセス許可を管理します
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f40b395bffabd089831a7a827a4ab81e216727c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734507"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory で管理単位のユーザーを追加して管理する

Azure Active Directory (Azure AD) では、きめ細かい管理スコープで制御するために、ユーザーを管理単位に追加できます。

## <a name="prerequisites"></a>前提条件

- 管理単位の各管理者に対する Azure AD Premium P1 または P2 ライセンス
- 管理単位のメンバーに対する Azure AD Free ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、「[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)」をご覧ください。

## <a name="add-users-to-an-administrative-unit"></a>管理単位にユーザー追加する

### <a name="azure-portal"></a>Azure portal

ユーザーは管理単位に個別に割り当てるか、一括操作で割り当てることができます。

- ユーザー プロファイルから個別に割り当てる:

   1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

   1. **[Azure Active Directory]**  >  **[ユーザー]** を選択し、管理単位に割り当てるユーザーを選択してユーザーのプロファイルを開きます。
   
   1. **[管理単位]** を選択します。 
   
   1. 1 つ以上の管理単位にユーザーを割り当てるには、 **[Assign to administrative unit]\(管理単位に割り当てる\)** を選択し、右側のペインで、ユーザーを割り当てる管理単位を選択します。

       ![ユーザーを管理単位に割り当てるための [管理単位] ペインのスクリーンショット。](./media/admin-units-add-manage-users/assign-users-individually.png)

- 管理単位から個別に割り当てる:

   1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

   1. **[Azure Active Directory]**  >  **[管理単位]** を選択し、ユーザーを割り当てる管理単位を選択します。

   1. **[すべてのユーザー]** を選択し、 **[メンバーの追加]** を選択します。 **[メンバーの追加]** ペインで、管理単位に割り当てる 1 人以上のユーザーを選択します。

        ![ユーザーを管理単位に割り当てるための 管理単位の [ユーザー] ペイン。](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- 一括操作としてユーザーを割り当てる:

   1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

   1. **[Azure Active Directory]**  >  **[管理単位]** の順に選択します。

   1. ユーザーを追加する管理単位を選択します。

   1. **[ユーザー]**  >  **[Bulk activities]\(一括アクティビティ\)**  >  **[Bulk add members]\(メンバーの一括追加\)** を選択します。 その後、コンマ区切り値 (CSV) テンプレートをダウンロードしてファイルを編集できます。 形式は単純であり、各行にユーザー プリンシパル名を 1 つ追加する必要があります。 ファイルの準備ができたら、適切な場所に保存した後、この手順の一環としてそれをアップロードします。

      ![一括操作としてユーザーを管理単位に割り当てるための [ユーザー] ペインのスクリーンショット。](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

PowerShell では、次の例の `Add-AzureADAdministrativeUnitMember` コマンドレットを使用して、ユーザーを管理単位に追加します。 ユーザーが追加される管理単位のオブジェクト ID と、追加するユーザーのオブジェクト ID が、引数として使用されます。 必要に応じて、実際の環境用に強調表示されているセクションを変更します。

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="microsoft-graph-api"></a>Microsoft Graph API

プレースホルダーをテスト情報に置き換え、次のコマンドを実行します。

要求

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

本文

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

例

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>ユーザーの管理単位の一覧を表示する

### <a name="azure-portal"></a>Azure portal

Azure portal では、次の操作を行うことでユーザーのプロファイルを開くことができます。

1. **Azure AD** に移動し、 **[ユーザー]** を選択します。

1. プロファイルを表示するユーザーを選択します。

1. **[管理単位]** を選択すると、ユーザーが割り当てられている管理単位が一覧表示されます。

   ![ユーザーが割り当てられている管理単位のスクリーンショット。](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

次のコマンドを実行します。

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> 既定では、`Get-AzureADAdministrativeUnitMember` では管理単位の 100 人のメンバーのみが返されます。 さらに多くのメンバーを取得するために、`"-All $true"` を追加できます。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

プレースホルダーをテスト情報に置き換え、次のコマンドを実行します。

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>管理単位から単一のユーザーを削除する

### <a name="azure-portal"></a>Azure portal

2 つの方法のいずれかで、管理単位からユーザーを削除できます。 

* Azure portal で、**Azure AD** に移動し、 **[ユーザー]** を選択します。 
  1. ユーザーを選択して、ユーザーのプロファイルを開きます。 
  1. ユーザーを削除する管理単位を選択し、 **[Remove from administrative unit] (管理単位から削除する)** を選択します。

     ![ユーザーのプロファイル ペインで管理単位からユーザーを削除する方法を示しているスクリーンショット。](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Azure portal で、**Azure AD** に移動し、 **[管理単位]** を選択します。
  1. ユーザーを削除する管理単位を選択します。 
  1. ユーザーを選択し、 **[メンバーの削除]** を選択します。
  
     ![管理単位レベルでユーザーを削除する方法を示しているスクリーンショット。](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

次のコマンドを実行します。

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

プレースホルダーをテスト情報に置き換え、次のコマンドを実行します。

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>一括操作としてユーザーを削除する

複数のユーザーを管理単位から削除するには、次の手順を実行します。

1. Azure portal で、**Azure AD** に移動します。

1. **[管理単位]** を選択し、ユーザーを削除する管理単位を選択できます。 

1. **[Bulk remove members]\(メンバーの一括削除\)** を選択し、削除するユーザーを一覧にするために使用する CSV テンプレートをダウンロードし ます。

   ![[ユーザー] ペインの [Bulk remove members]\(メンバーの一括削除\) リンクを示すスクリーンショット。](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. ダウンロードした CSV テンプレートを関連するユーザー エントリで編集します。 テンプレートの最初の 2 行は削除しないでください。 各行に 1 つのユーザー プリンシパル名 (UPN) を追加します。

   ![管理単位から一括でユーザーを削除するための編集された CSV ファイルのスクリーンショット。](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. 変更を保存し、ファイルをアップロードし、 **[送信]** を選択します。

## <a name="next-steps"></a>次のステップ

- [管理単位にロールを割り当てる](admin-units-assign-roles.md)
- [管理単位にグループを追加する](admin-units-add-manage-groups.md)
