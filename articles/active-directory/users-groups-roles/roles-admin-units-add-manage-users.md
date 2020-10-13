---
title: 管理単位のユーザーを追加、削除、一覧表示する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位のユーザーとそのロールのアクセス許可を管理します
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d29f4ef5806eb8ed9385696dea78f4ae0992b93
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818154"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory で管理単位のユーザーを追加して管理する

Azure Active Directory (Azure AD) では、きめ細かい管理スコープで制御するために、ユーザーを管理単位 (AU) に追加できます。

管理単位の管理に PowerShell や Microsoft Graph を使用するよう準備する手順については、「[作業の開始](roles-admin-units-manage.md#get-started)」を参照してください。

## <a name="add-users-to-an-au"></a>AU にユーザーを追加する

### <a name="azure-portal"></a>Azure portal

ユーザーは管理単位に個別に割り当てるか、一括操作で割り当てることができます。

- ユーザー プロファイルから個別に割り当てる

   1. 特権ロール管理者のアクセス許可で [Azure AD 管理センター](https://portal.azure.com)にサインインします。
   1. **[ユーザー]** を選択し、管理単位に割り当てるユーザーを選択し、ユーザーのプロファイルを開きます。
   1. **[管理単位]** を選択します。 ユーザーを 1 つまたは複数の管理単位に割り当てるには、 **[管理単位に割り当てる]** を選択し、ユーザーが割り当てられる管理単位を選択します。

       ![[追加] を選択してから、管理単位の名前を入力する](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

- 管理単位からの個別に割り当てる

   1. 特権ロール管理者のアクセス許可で [Azure AD 管理センター](https://portal.azure.com)にサインインします。
   1. **[管理単位]** を選択し、ユーザーを割り当てる管理単位を選択します。
   1. **[すべてのユーザー]** を選択し、 **[メンバーの追加]** を選択し、管理単位に割り当てる 1 つまたは複数のユーザーを **[メンバーの追加]** ウィンドウから選択します。

        ![管理単位を選択して、[メンバーの追加] を選択する](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

- 一括割り当て

   1. 特権ロール管理者のアクセス許可で [Azure AD 管理センター](https://portal.azure.com)にサインインします。
   1. **[管理単位]** を選択します。
   1. ユーザーが追加される管理単位を選択します。
   1. **[すべてのユーザー]** 、 **[.csv ファイルのメンバーの追加]** の順に開きます。 その後、コンマ区切り値 (CSV) テンプレートをダウンロードしてファイルを編集できます。 この形式は単純で、各行にユーザー プリンシパル名を 1 つ追加する必要があります。 ファイルの準備ができたら、適切な場所に保存した後、この手順の一環としてアップロードします。

    ![管理単位にユーザーを一括割り当てする](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

上記の例では、コマンドレット Add-AzureADAdministrativeUnitMember を使用して、ユーザーを管理単位に追加します。 ユーザーが追加される管理単位のオブジェクト ID と、追加するユーザーのオブジェクト ID が、引数として受け取られます。 強調表示されたセクションは、それぞれの環境で必要に応じて変更される場合があります。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

例:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>ユーザーの管理単位を一覧表示する

### <a name="azure-portal"></a>Azure portal

Azure portal によって、次の操作でユーザーのプロファイルを開くことができます。

1. **[Azure AD]** 、 **[ユーザー]** の順に開きます。

1. ユーザーを選択して、ユーザーのプロファイルを開きます。

1. **[管理単位]** を選択すると、ユーザーが割り当てられている管理単位が一覧表示されます。

   ![ユーザーの管理単位を一覧表示する](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
注:既定では、Get-AzureADAdministrativeUnitMember を実行すると、返されるメンバーの数が 100 に限定されます。"-All $true" を追加することで、取得されるメンバーの数を増やすことができます。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>AU から 1 人のユーザーを 削除する

### <a name="azure-portal"></a>Azure portal

管理単位からユーザーを削除する方法は 2 つあります。 Azure portal で、**Azure AD** >  **[ユーザー]** の順にアクセスして、ユーザーのプロファイルを開くことができます。 ユーザーを選択して、ユーザーのプロファイルを開きます。 ユーザーが削除される管理単位を選択してから、 **[Remove from administrative unit] (管理単位から削除する)** を選択します。

![ユーザー プロファイルの管理単位からユーザーを削除する](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

また、**Azure AD** >  **[管理単位]** で、ユーザーが削除される管理単位を選択して、ユーザーを削除することもできます。 ユーザーを選択し、 **[メンバーの削除]** を選択します。
  
![管理単位レベルでユーザーを削除する](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/ $ref

## <a name="bulk-remove-more-than-one-user"></a>複数のユーザーを一括削除する

Azure AD > [管理単位] の順にアクセスし、ユーザーが削除される管理単位を選択できます。 [Bulk remove member (メンバーの一括削除)] をクリックします。 削除するユーザーの一覧を指定するための CSV テンプレートをダウンロードします。

ダウンロードした CSV テンプレートを関連するユーザー エントリで編集します。 テンプレートの最初の 2 行を削除しないでください。 各行にユーザーの UPN を 1 つずつ追加します。

![管理単位からユーザーを一括削除するための CSV ファイルを編集する](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

エントリをファイルに保存したら、ファイルをアップロードして、 **[送信]** を選択します。

![一括アップロードファイルを送信する](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>次のステップ

- [管理単位にロールを割り当てる](roles-admin-units-assign-roles.md)
- [管理単位にグループを追加する](roles-admin-units-add-manage-groups.md)
