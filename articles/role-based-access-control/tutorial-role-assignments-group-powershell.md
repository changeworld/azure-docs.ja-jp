---
title: 'チュートリアル: RBAC と Azure PowerShell を使用してグループにアクセス権を付与する | Microsoft Docs'
description: Azure PowerShell を使用して、ロールベースのアクセス制御 (RBAC) によって、サブスクリプション内のすべてを表示し、リソース グループ内のすべてを管理するためのアクセス権をグループに付与します。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 5012ce22204c3ac32b22fe2cd842ce1300b426f8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295815"
---
# <a name="tutorial-grant-access-for-a-group-using-rbac-and-azure-powershell"></a>チュートリアル: RBAC と Azure PowerShell を使用してグループにアクセス権を付与する

[ロールベースのアクセス制御 (RBAC)](overview.md) とは、Azure に存在するリソースに対するアクセス権を管理するための手法です。 このチュートリアルでは、Azure PowerShell を使用して、サブスクリプション内のすべてを表示し、リソース グループ内のすべてを管理するためのアクセス権をグループに付与します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * さまざまなスコープでのグループへのアクセス権の付与
> * アクセス権の表示
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- Azure Active Directory でグループを作成する (または既存のグループを所有する) ためのアクセス許可
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>ロールの割り当て

RBAC では、アクセス権を付与するには、ロールの割り当てを作成する必要があります。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 このチュートリアルで実行するロールの割り当ては次の 2 つです。

| セキュリティ プリンシパル | ロール定義 | スコープ |
| --- | --- | --- |
| グループ<br>(RBAC チュートリアル グループ) | [Reader](built-in-roles.md#reader) | サブスクリプション |
| グループ<br>(RBAC チュートリアル グループ)| [Contributor](built-in-roles.md#contributor) | リソース グループ<br>(rbac-tutorial-resource-group) |

   ![グループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>グループの作成

ロールを割り当てるには、ユーザー、グループ、またはサービス プリンシパルが必要です。 グループがない場合は、作成します。

- Azure Cloud Shell で、[New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) コマンドを使用して、新しいグループを作成します。

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

グループを作成するアクセス許可がない場合は、代わりに「[チュートリアル: RBAC と Azure PowerShell を使用してユーザーにアクセス権を付与する](tutorial-role-assignments-user-powershell.md)」をお試しください。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループを使用して、リソース グループ スコープでロールを割り当てる方法を示すことができます。

1. [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) コマンドを使用して、リージョンの場所の一覧を取得します。

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. 近くの場所を選択し、変数に割り当てます。

   ```azurepowershell
   $location = "westus"
   ```

1. [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドを使用して、新しいリソース グループを作成します。

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>アクセス権の付与

グループにアクセス権を付与するには、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) コマンドを使用してロールを割り当てます。 セキュリティ プリンシパル、ロールの定義、およびスコープを指定する必要があります。

1. [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) コマンドを使用して、グループのオブジェクト ID を取得します。

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. グループのオブジェクト ID を変数に保存します。

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. [Get-AzureRmSubscription](/powershell/module/azurerm.resources/get-azurermsubscription) コマンドを使用して、サブスクリプションの ID を取得します。

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. サブスクリプション スコープを変数に保存します。

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. [閲覧者](built-in-roles.md#reader)ロールをサブスクリプション スコープでグループに割り当てます。

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. [共同作成者](built-in-roles.md#contributor)ロールをリソース グループ スコープでグループに割り当てます。

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>アクセス権の表示

1. サブスクリプションのアクセス権を確認するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    出力では、閲覧者ロールが、サブスクリプション スコープで RBAC チュートリアル グループに割り当てられていることを確認できます。

1. リソース グループのアクセス権を確認するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    出力では、共同作成者ロールと閲覧者ロールの両方が、RBAC チュートリアル グループに割り当てられていることを確認できます。 共同作成者ロールは rbac-tutorial-resource-group スコープです。また、閲覧者ロールは、サブスクリプション スコープに継承されます。

## <a name="optional-list-access-using-the-azure-portal"></a>(省略可能) Azure portal を使用したアクセス権の表示

1. Azure portal で、ロールの割り当てがどのように表示されるかを確認するには、サブスクリプションの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![サブスクリプション スコープでのグループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. リソース グループの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![リソース グループ スコープでのグループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>アクセス権の削除

ユーザー、グループ、アプリケーションのアクセス権を削除するには、[Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) を使用して、ロールの割り当てを削除します。

1. 次のコマンドを使用して、リソース グループ スコープでグループの共同作成者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 次のコマンドを使用して、サブスクリプション スコープでグループの閲覧者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースをクリーンアップするには、リソース グループとグループを削除します。

1. [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループを削除します。

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. 確認を求められたら、「**Y**」と入力します。削除には数秒かかります。

1. [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup) コマンドを使用して、グループを削除します。

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    グループを削除しようしたときにエラーが発生する場合は、ポータルでもグループを削除できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [RBAC と PowerShell を使用してアクセスを管理する](role-assignments-powershell.md)