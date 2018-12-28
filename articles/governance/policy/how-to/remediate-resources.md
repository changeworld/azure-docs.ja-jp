---
title: 準拠していないリソースを修復する
description: このハウツーでは、Azure Policy のポリシーに準拠していないリソースを修復する手順を説明します。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 093b49bea167efb12b941f8f0baff6fbdae5be25
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312648"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure Policy を使って準拠していないリソースを修復する

**deployIfNotExists**に準拠していないリソースは､**修復**を使って準拠状態にすることができます。 修復は､既存のリソースに対して割り当てポリシーの **deployIfNotExists** 効果を実行するよう Policy に指示することによって行うことができます｡ この記事では、Policy による修復を理解して実行するために必要な手順を示します。

## <a name="how-remediation-security-works"></a>修復のセキュリティの仕組み

**deployIfNotExists** ポリシー定義にあるテンプレートを実行する場合､Policy は[管理対象 id](../../../active-directory/managed-identities-azure-resources/overview.md)を使用します｡
管理対象 ID は Policy によって各割り当てに作成されますが、どのようなロールを管理対象 ID に付与するかについての詳細が必要です。 管理対象 ID にロールが存在しない場合、そのポリシーまたはイニシアチブの割り当て中にこのエラーが表示されます。 ポータルを使用している場合、割り当てが開始されると、Policy は示されているロールのすべてを自動的に管理対象 ID に付与します。

![管理対象 ID - ロールが存在しない](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> **deployIfNotExists** で変更されたリソースがポリシー割り当てのスコープの範囲外の場合､あるいはテンプレートがポリシー割り当てのスコープの範囲外にあるリソース上のプロパティにアクセスした場合､その割り当ての管理対象 ID には[手動でアクセス権を付与](#manually-configure-the-managed-identity)する必要があります｡さもないと､修復デプロイは失敗します。

## <a name="configure-policy-definition"></a>ポリシー定義を設定する

最初の手順は、含まれているテンプレートのコンテンツを **deployIfNotExists** が正しくデプロイするために必要なロールをポリシー定義に定義することです｡ **details (詳細)** プロパティで **roleDefinitionIds** プロパティを追加します。 このプロパティは、環境にあるロールに合致する一連の文字列です。 [deployIfNotExists の例](../concepts/effects.md#deployifnotexists-example)に示す例を参照してください｡

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** は完全なリソース識別子を使用し、ロールの短い **roleName** は使用しません｡ 環境にある「共同作成者」ロールの ID を取得するには、次のコードを使用します。

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>管理対象 ID を手動で設定します。

ポータルを使用して割り当てを作成する際、Policy はポリシー管理対象 ID を作成すると共に､**roleDefinitionIds** に定義されているロールをその管理対象 ID に付与します。 次の条件では、管理対象 ID を作成し、アクセス許可を割り当てる手順を手動で行う必要があります:

- SDK (Azure PowerShell など) を使用している場合
- 割り当てスコープの範囲外のリソースがテンプレートによって変更される場合
- 割り当てスコープの範囲外のリソースがテンプレートによって読み取られる場合

> [!NOTE]
> 現在この機能をサポートしている SDK は Azure PowerShell と .NET だけです｡

### <a name="create-managed-identity-with-powershell"></a>PowerShell で管理対象 ID を作成する

ポリシーの割り当て時に管理対象 ID を作成するには､**場所** を定義して､**AssignIdentity** を使用する必要があります｡ 次の例では、組み込みポリシー **Deploy SQL DB transparent data encryption** の定義を取得して､ターゲット リソース グループを設定し、割り当てを作成しています。

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzureRmPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzureRmResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzureRmPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

この結果､`$assignment` 変数には管理対象 ID のプリンシパル ID とともに､ポリシー割り当てを作成するときに返される標準値が含まれます｡ この変数には `$assignment.Identity.PrincipalId` を使ってアクセスできます｡

### <a name="grant-defined-roles-with-powershell"></a>定義したロールを PowerShell を使って付与する

新しい管理対象 ID に必要なロールを付与するには､Azure Active Directory からレプリケーションを完了しておく必要があります。 次の例では、レプリケーションを完了した後､**roleDefinitionIds** に対して `$policyDef` でポリシー定義を反復処理し､[New-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用して､新しい管理対象 ID にロールを付与します｡

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzureRmRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>定義したロールをポータルから付与する

ポータルを使用して､割り当ての管理対象 ID に定義したロールを付与する方法は 2 通りあります｡1 つは **アクセス制御 (IAM)** を利用する方法､もう 1 つはポリシーまたはイニシアティブ割り当てを編集する方法です(最後に **保存** をクリック)。

割り当ての管理対象 ID にロールを追加するには、次の手順に従います｡

1. Azure portal 上で **[すべてのサービス]** をクリックし、**[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[割り当て]** を選択します。

1. 管理対象 ID がある割り当てを見つけて、その名前をクリックします。

1. 編集ページで**割り当て ID** を見つけます｡ 割り当て ID は以下のような ID です。

   ```
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   管理対象 ID の名前は、割り当てリソース ID の最後の部分です｡この例では `2802056bfc094dfb95d4d7a5` です。 割り当てリソース ID のこの部分をコピーします。

1. ロールの定義で手動で追加する必要があるリソース､またはリソースの親コンテナー (リソース グループ、サブスクリプション、管理グループ) に移動します。

1. リソース ページにある **アクセス制御 (IAM)** のリンクをクリックして、アクセス制御ページの上部にある **[+ Add role assignment]\(+ ロール割り当ての追加\)** をクリックします。

1. ポリシー定義から､ **roleDefinitionIds** に一致するロールを選択します｡ **Assign access to** は ''Azure AD user, group, or application という既定値の設定のままにします｡ **[選択]** ボックスで、前の手順で見つけた割り当てリソース ID の部分を貼り付けるか､入力します。 検索が完了したら、同じ名前のオブジェクトをクリックすることで ID を選択し､**[保存]** をクリックします。

## <a name="create-a-remediation-task"></a>修復タスクを作成する

評価では､**deployIfNotExists** 効果を持つポリシー割り当てによって､準拠していないリソースがあるかどうかが判定されます｡ 準拠していないリソースが見つかった場合は、**修復**ページにその詳細が表示されます。 準拠していないリソースがあるポリシーの一覧には、**修復タスク** をトリガーするオプションがあります｡ このオプションは､**deployIfNotExists** テンプレートからデプロイを作成するものです。

**修復タスク** を作成するには､次の手順に従います。

1. Azure portal 上で **[すべてのサービス]** をクリックし、**[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

   ![ポリシーを検索する](../media/remediate-resources/search-policy.png)

1. Azure Policy ページの左側にある **[修復]** を選択します。

   ![修復の選択](../media/remediate-resources/select-remediation.png)

1. 準拠していないリソースがある **deployIfNotExists** ポリシー割り当てはすべて､**Policies to remediate**タブとデータ テーブルに含まれます。 準拠していないリソースがあるポリシーをクリックします。 **新しい修復タスク**ページが開きます。

   > [!NOTE]
   > **修復タスク**ページは、**コンプライアンス** ページから問題のポリシーを見つけて､クリックし、ページの [] をクリックし、**修復タスクの作成**ボタンをクリックしても開くことができます。

1. **[New remediation task]\(新しい修復タスク\)** ページで **[Scope]\(スコープ\)** 省略記号ボタンを使って、ポリシーが割り当てられている子リソースを選択することで、修復するリソースをフィルター処理します (個々のリソース オブジェクトまでフィルター可能)。 また、**[場所]** ドロップダウンを使って､リソースをさらにフィルター処理することもできます｡ 表に示されたリソースのみ修復されます。

   ![修復 - リソースの選択](../media/remediate-resources/select-resources.png)

1. リソースのフィルター処理を終えたら、**[修復]** をクリックして、修復タスクを開始します。 **[修復タスク]** タブに対するポリシー コンプライアンス ページが開いて、タスクの進行状況が表示されます｡

   ![修復 - タスクの進行状況](../media/remediate-resources/task-progress.png)

1. ポリシー コンプライアンス ページの **[修復タスク]** をクリックすると､進行状況の詳細が表示されます｡ 修復されているリソースの一覧と共に、タスクに使用されたフィルターが表示されます。

1. **[修復タスク]** ページで、修復タスクを右クリックすると、その修復タスクのデプロイまたはリソースのいずれかが表示されます。 行の末尾にあると **[関連イベント]** をクリックすると､エラー メッセージなどの詳細が表示されます｡

   ![修復 - リソース タスクのコンテキスト メニュー](../media/remediate-resources/resource-task-context-menu.png)

**[修復タスク]** を使ってデプロイされたリソースは、ポリシー コンプライアンス ページの **[デプロイされたリソース]** タブに追加されます。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認する
- [ポリシー定義の構造](../concepts/definition-structure.md)を確認する
- [ポリシーの効果について](../concepts/effects.md)確認する
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解する
- [コンプライアンス データを取得する](getting-compliance-data.md)ための方法を学びます。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。