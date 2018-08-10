---
title: 管理グループを変更、削除、または管理する方法 - Azure | Microsoft Docs
description: 管理グループ階層をメンテナンスおよび更新する方法を説明します。
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: 04d5f5dc340d6be47a26baf05aa3d2f2ea7458ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428820"
---
# <a name="manage-your-resources-with-management-groups"></a>管理グループを使用してリソースを管理する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを変更、削除、管理して、[Azure Policy](../azure-policy/azure-policy-introduction.md) と [Azure ロール ベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) で使用できる階層を作成できます。 管理グループの詳細については、「[Azure 管理グループでリソースを整理する](management-groups-overview.md)」を参照してください。

管理グループに変更を加えるには、管理グループに対する所有者または共同作成者ロールが必要です。 どのアクセス許可があるかを確認するには、管理グループを選択し、**[IAM]** を選択します。 RBAC ロールについて詳しくは、[RBAC を使用したアクセスとアクセス許可の管理](../role-based-access-control/overview.md)に関する記事をご覧ください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>管理グループの名前を変更する

管理グループの名前を変更するには、ポータル、PowerShell、または Azure CLI を使用します。

### <a name="change-the-name-in-the-portal"></a>ポータルでの名前の変更

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します  
3. 名前を変更する管理グループを選択します。
4. ページの上部にある **[グループ名の変更]** オプションを選択します。

    ![グループ名の変更](media/management-groups/detail_action_small.png)
5. メニューが開いたら、表示する新しい名前を入力します。

    ![グループ名の変更](media/management-groups/rename_context.png)
6. **[保存]** を選択します。

### <a name="change-the-name-in-powershell"></a>PowerShell での名前の変更

表示名を更新するには、**Update-AzureRmManagementGroup** を使用します。 たとえば、管理グループ名を "Contoso IT" から "Contoso Group" に変更するには、次のコマンドを実行します。 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Azure CLI での名前の変更

Azure CLI を使用する場合は、update コマンドを使用します。

```azurecli-interactive
az account management-group update --name Contoso --display-name "Contoso Group" 
```

---

## <a name="delete-a-management-group"></a>管理グループの削除

管理グループを削除するには、次の要件を満たす必要があります。

1. 管理グループの下に子管理グループやサブスクリプションがないこと。
    - サブスクリプションを管理グループ外に移動する場合は、[別の管理グループへのサブスクリプションの移動](#Move-subscriptions-in-the-hierarchy)に関する記事を参照してください。
    - 管理グループを別の管理グループに移動するには、[階層内での管理グループの移動](#Move-management-groups-in-the-hierarchy)に関する記事をご覧ください。
2. 管理グループに対する書き込みアクセス許可、管理グループの所有者または共同作成者ロールが必要です。 どのアクセス許可があるかを確認するには、管理グループを選択し、**[IAM]** を選択します。 RBAC ロールについて詳しくは、[RBAC を使用したアクセスとアクセス許可の管理](../role-based-access-control/overview.md)に関する記事をご覧ください。  

### <a name="delete-in-the-portal"></a>ポータルでの削除

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します  
3. 削除する管理グループを選択します。
4. **[削除]** を選択します。
    - アイコンが無効になっている場合は、アイコンの上にマウス セレクターを置くと理由が表示されます。

    ![グループの削除](media/management-groups/delete.png)
5. 管理グループを削除することを確認するウィンドウが開きます。

    ![グループの削除](media/management-groups/delete_confirm.png)
6. **[はい]** を選択します


### <a name="delete-in-powershell"></a>PowerShell での削除

PowerShell で **Remove-AzureRmManagementGroup** コマンドを使用して、管理グループを削除します。 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Azure CLI での削除

Azure CLI で、az account management-group delete コマンドを使用します。

```azurecli-interactive
az account management-group delete --name Contoso
```
---

## <a name="view-management-groups"></a>管理グループの表示

直接または継承された RBAC ロールがある管理グループを表示することができます。  

### <a name="view-in-the-portal"></a>ポータルでの表示

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します 
3. 管理グループの階層ページに、アクセスできるすべての管理グループとサブスクリプションが読み込まれ、それらを調べることができます。 グループ名を選択すると、階層の下位レベルに移動します。 ナビゲーションは、ファイル エクスプローラーと同じように機能します。 
    ![メイン](media/management-groups/main.png)
4. 管理グループの詳細を表示するには、管理グループのタイトルの横にある **[(詳細])** リンクを選択します。 このリンクを使用できない場合は、その管理グループを表示するアクセス許可がありません。  

### <a name="view-in-powershell"></a>PowerShell での表示

すべてのグループを取得するには、Get-AzureRmManagementGroup コマンドを使用します。  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
1 つの管理グループの情報の場合は、-GroupName パラメーターを使用します

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Azure CLI での表示

すべてのグループを取得するには、list コマンドを使用します。  

```azurecli-interactive
az account management-group list
```
1 つの管理グループの情報の場合は、show コマンドを使用します

```azurecli-interactive
az account management-group show --name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>階層内でのサブスクリプションの移動

管理グループを作成する理由の 1 つは、サブスクリプションをバンドルするためです。 管理グループとサブスクリプションのみが別の管理グループの子になることができます。 管理グループに移動するサブスクリプションは、親管理グループからすべてのユーザー アクセスとポリシーを継承します。

サブスクリプションを移動するには、いくつかのアクセス許可が必要です。

- 子サブスクリプションに対する "所有者" ロール。
- 新しい親管理グループに対する "所有者" または "作成者" ロール。
- 以前の親管理グループに対する "所有者" または "作成者" ロール。

どのアクセス許可があるかを確認するには、管理グループを選択し、**[IAM]** を選択します。 RBAC ロールについて詳しくは、[RBAC を使用したアクセスとアクセス許可の管理](../role-based-access-control/overview.md)に関する記事をご覧ください。

### <a name="move-subscriptions-in-the-portal"></a>ポータルでのサブスクリプションの移動

**既存のサブスクリプションを管理グループに追加する**

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します
3. 親にする予定の管理グループを選択します。
4. ページの上部で、**[サブスクリプションの追加]** を選択します。
5. 一覧で、正しい ID を持つサブスクリプションを選択します。

    ![子](media/management-groups/add_context_sub.png)
1. [保存] を選択します

**管理グループからサブスクリプションを削除する**

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します 
3. 現在親となっている管理グループを選択します。  
4. 一覧内で移動するサブスクリプションの行末にある省略記号を選択します。

    ![Move](media/management-groups/move_small.png)
5. **[移動]** を選択します
6. 開かれたメニューで、**親管理グループ**を選択します。

    ![Move](media/management-groups/move_small_context.png)
7. **[保存]** を選びます。

### <a name="move-subscriptions-in-powershell"></a>PowerShell でのサブスクリプションの移動

PowerShell でサブスクリプションを移動するには、Add-AzureRmManagementGroupSubscription コマンドを使用します。  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

サブスクリプションと管理グループ間のリンクを削除するには、Remove-AzureRmManagementGroupSubscription コマンドを使用します。

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Azure CLI でのサブスクリプションの移動

CLI でサブスクリプションを移動するには、add コマンドを使用します。

```azurecli-interactive
az account management-group subscription add --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

管理グループからサブスクリプションを削除するには、サブスクリプションの remove コマンドを使用します。  

```azurecli-interactive
az account management-group subscription remove --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>階層内での管理グループの移動  

親管理グループを移動すると、管理グループ、サブスクリプション、リソース グループ、リソースなどのすべての子リソースが親と共に移動されます。   

### <a name="move-management-groups-in-the-portal"></a>ポータルでの管理グループの移動

1. [Azure Portal](https://portal.azure.com) にログインします
2. **[すべてのサービス]** > **[管理グループ]** を選択します
3. 親にする予定の管理グループを選択します。
5. ページの上部で、**[管理グループの追加]** を選択します。
6. 開いたメニューで、管理グループを新規作成するか、既存のものを使用するかを選択します。
    - 新規作成を選んだ場合、新しい管理グループが作成されます。
    - 既存のものを使用することを選んだ場合は、この管理グループに移動できるすべての管理グループのドロップダウンが表示されます。  

    ![移動](media/management-groups/add_context_MG.png)
7. **[保存]** を選びます。

### <a name="move-management-groups-in-powershell"></a>PowerShell での管理グループの移動

PowerShell の Update-AzureRmManagementGroup コマンドを使用して、管理グループを別のグループの下に移動します。  

```powershell
Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  

### <a name="move-management-groups-in-azure-cli"></a>Azure CLI での管理グループの移動

Azure CLI で管理グループを移動するには update コマンドを使用します。

```azurecli-interactive
az account management-group update --name Contoso --parent "Contoso Tenant"
```

---

## <a name="next-steps"></a>次の手順

管理グループについて詳しくは、以下をご覧ください。

- [Azure 管理グループでリソースを整理する](management-groups-overview.md)
- [管理グループを作成して Azure リソースを整理する](management-groups-create.md)
- [Azure PowerShell モジュールのインストール](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 仕様の確認](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Azure CLI 拡張機能のインストール](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
