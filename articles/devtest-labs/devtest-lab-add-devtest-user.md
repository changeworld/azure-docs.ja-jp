---
title: 所有者とユーザーを追加する
description: Azure Portal または PowerShell を使用した Azure DevTest Labs での所有者とユーザーの追加
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6edf548658d02ae3427fe5ac448dcc3e38a6b4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068762"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Azure DevTest Labs での所有者とユーザーの追加
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Azure DevTest Labs でのアクセスは、[Azure のロール ベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) で制御します。 Azure RBAC を使用すると、チーム内で職務を "*ロール*" に分離し、各自の職務を実行するために必要な量のアクセスのみをユーザーに付与します。 これらの Azure ロールのうちの 3 つは、"*所有者*"、"*DevTest Labs ユーザー*"、"*共同作成者*" です。 この記事では、これら 3 つの主な Azure ロールそれぞれで実行できるアクションについて説明します。 ポータルを経由して、および PowerShell スクリプトを使用してラボにユーザーを追加する方法、およびサブスクリプション レベルでユーザーを追加する方法について説明します。

## <a name="actions-that-can-be-performed-in-each-role"></a>各ロールで実行できるアクション
次の 3 つの主なロールをユーザーを割り当てることができます。

* 所有者
* DevTest Labs User
* Contributor

次の表は、これらの各ロールのユーザーが実行できるアクションを示しています。

| **このロールのユーザーが実行できるアクション** | **DevTest Labs User** | **[所有者]** | **Contributor** |
| --- | --- | --- | --- |
| **ラボのタスク** | | | |
| ラボへのユーザーの追加 |いいえ |はい |いいえ |
| コストの設定の更新 |いいえ |はい |はい |
| **VM ベースのタスク** | | | |
| カスタム イメージの追加と削除 |いいえ |はい |はい |
| 数式の追加、更新、および削除 |はい |はい |はい |
| Azure Marketplace イメージの有効化 |いいえ |はい |はい |
| **VM タスク** | | | |
| VM の作成 |はい |はい |はい |
| VM の開始、停止、および削除 |ユーザーによって作成された VM のみ |はい |はい |
| VM のポリシーの更新 |いいえ |はい |はい |
| データ ディスクの VM への追加と VM からの削除 |ユーザーによって作成された VM のみ |はい |はい |
| **アーティファクトのタスク** | | | |
| アーティファクトのリポジトリの追加と削除 |いいえ |はい |はい |
| アーティファクトの適用 |はい |はい |はい |

> [!NOTE]
> ユーザーが VM を作成すると、そのユーザーは作成された VM の **所有者** ロールに自動的に割り当てられます。
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>ラボ レベルでの所有者またはユーザーの追加
所有者とユーザーは Azure Portal 経由で、ラボレベルで追加できます。 ユーザーには、有効な [Microsoft アカウント (MSA)](./devtest-lab-faq.yml)を持つ外部ユーザーも可能です。
次のステップでは、Azure DevTest Labs で所有者またはユーザーをラボに追加するプロセスについて説明します。

1. ユーザー アクセス管理者[またはAzure portal](https://portal.azure.com)[としてサインイン](../role-based-access-control/built-in-roles.md#user-access-administrator)[します](../role-based-access-control/built-in-roles.md#owner)。

1. 目的のリソース グループを開き、 **[DevTest Labs] を選択します**。

1. ナビゲーション メニューで **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロール割り当ての追加]** の順に選択します。

    ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. [ロール **] タブ** で、[所有者] または **[ユーザー] ロール** **を選択** します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. [メンバー **] タブ** で、目的のロールを与えるユーザーを選択します。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。


## <a name="add-an-external-user-to-a-lab-using-powershell"></a>PowerShell を使用して、ラボに外部ユーザーを追加します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Portal にユーザーを追加するだけでなく、PowerShell スクリプトを使用して、ラボに外部ユーザーを追加することもできます。 次の例で、**Values to change** コメントの下のパラメーター値を変更してください。
`subscriptionId`、`labResourceGroup`、および `labName` の値は、Azure Portal のラボのブレードから取得することができます。

> [!NOTE]
> サンプル スクリプトでは、指定したユーザーが Active Directory にゲストとして追加されていると想定しており、そうではない場合はスクリプトが失敗します。 Active Directory にないユーザーをラボに追加するには、セクション「 [ラボ レベルでの所有者またはユーザーの追加](#add-an-owner-or-user-at-the-lab-level)」で示されているように、Azure Portal を使用してユーザーをロールに割り当てます。   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>サブスクリプション レベルでの所有者またはユーザーの追加
Azure のアクセス許可は、Azure で親スコープから子スコープに反映されます。 したがって、ラボを含む Azure サブスクリプションの所有者は、自動的にこれらのラボの所有者となります。 また、これらの所有者は、VM やラボのユーザーによって作成されたその他のリソース、および Azure DevTest Labs サービスも所有します。 

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)でラボのブレードを使用してラボに他の所有者を追加できます。 ただし、追加された所有者の管理のスコープは、サブスクリプションの所有者のスコープよりも狭くなります。 たとえば、追加された所有者には、DevTest Labs サービスによって、サブスクリプションで作成されたリソースの一部へのフル アクセスがありません。 

Azure サブスクリプションに所有者を追加するには、次の手順を実行します。

1. ユーザー アクセス管理者[またはAzure portal](https://portal.azure.com)[としてサインイン](../role-based-access-control/built-in-roles.md#user-access-administrator)[します](../role-based-access-control/built-in-roles.md#owner)。

1. 目的のサブスクリプション グループを開きます。

1. ナビゲーション メニューで **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロール割り当ての追加]** の順に選択します。

    ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. [ロール] **タブ** で、[所有者] ロール **を選択** します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **[メンバー]** タブで、所有者ロールを付与するユーザーを選択します。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
