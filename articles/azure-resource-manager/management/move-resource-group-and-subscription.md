---
title: リソースを新しいサブスクリプションまたはリソース グループに移動する
description: Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。
ms.topic: conceptual
ms.date: 06/03/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ec23b4306f088328bfb72f3cf9071a70f8eb2307
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586781"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>リソースを新しいリソース グループまたはサブスクリプションに移動する

この記事では、Azure リソースを別の Azure サブスクリプションまたは同じサブスクリプションの別のリソース グループに移動する方法について説明します。 リソースの移動には、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用できます。

移動操作の間は、ソース グループとターゲット グループの両方がロックされます。 これらのリソース グループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。 このロックはリソース グループでリソースを追加、更新、削除できなくなることを意味します。 リソースが停止されるわけではありません。 たとえば、Azure SQL 論理サーバーとそのデータベースを新しいリソース グループまたはサブスクリプションに移動した場合、そのデータベースを使用するアプリケーションでダウンタイムは発生しません。 引き続きデータベースの読み取りと書き込みを行うことができます。 ロックは最大 4 時間継続できますが、ほとんどの移動は、はるかに短い時間で完了します。

リソースを移動しても、新しいリソース グループまたはサブスクリプションに移動されるだけです。 リソースの場所は変わりません。

## <a name="changed-resource-id"></a>変更されたリソース ID

リソースを移動すると、そのリソース ID は変更されます。 リソース ID の標準形式は `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` です。 リソースを新しいリソース グループまたはサブスクリプションに移動するときは、そのパス内の 1 つ以上の値を変更します。

リソース ID を任意の場所で使用する場合、その値を変更する必要があります。 たとえば、ポータル内にリソース ID を参照する[カスタム ダッシュボード](../../azure-portal/quickstart-portal-dashboard-azure-cli.md)がある場合は、その値を更新する必要があります。 新しいリソース ID のために更新する必要があるスクリプトまたはテンプレートを探します。

## <a name="checklist-before-moving-resources"></a>リソースの移動前のチェック リスト

リソースを移動する前に行うべき重要な手順がいくつかあります。 これらの条件を確認することにより、エラーの発生を回避できます。

1. 移動するリソースは、移動操作をサポートしているリソースである必要があります。 移動をサポートしているリソースのリストは、「[Move operation support for resources](move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。

1. 一部のサービスには、リソースを移動するときに特定の制限または要件があります。 次のサービスのいずれかを移動する場合は、移動の前にそのガイダンスを確認してください。

   * Azure Stack Hub を使用している場合は、グループ間でリソースを移動することはできません。
   * [App Services の移動ガイダンス](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services の移動ガイダンス](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [クラシック デプロイ モデルの移動ガイダンス](./move-limitations/classic-model-move-limitations.md) - Classic Compute、Classic Storage、Classic Virtual Networks、Cloud Services
   * [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)
   * [Recovery Services の移動ガイダンス](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [仮想マシンの移動ガイダンス](./move-limitations/virtual-machines-move-limitations.md)
   * Azure サブスクリプションを新しい管理グループに移動するには、「[サブスクリプションの移動](../../governance/management-groups/manage.md#move-subscriptions)」を参照してください。

1. Azure ロールがリソース (または子リソース) に直接割り当てられているリソースを移動する場合、ロールの割り当ては移動されず、孤立します。 移動した後は、ロールの割り当てを再作成する必要があります。 最終的に、孤立したロールの割り当ては自動的に削除されますが、移動する前にロールの割り当てを削除することをお勧めします。

    ロールの割り当てを管理する方法の詳細については、[Azure ロールの割り当ての一覧表示](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope)に関するセクションおよび [Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)方法に関するページを参照してください。

1. 移動元と移動先のサブスクリプションがアクティブである必要があります。 無効になっているアカウントを有効にするときに問題がある場合は、[Azure サポート リクエストを作成します](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 問題の種類として **[サブスクリプション管理]** を選択します。

1. 移動元と移動先のサブスクリプションが同じ [Azure Active Directory テナント](../../active-directory/develop/quickstart-create-new-tenant.md)内に存在している必要があります。 両方のサブスクリプションに同じテナント ID があることを確認するには、Azure PowerShell または Azure CLI を使用します。

   Azure PowerShell では、次を使用します。

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI では、次を使用します。

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   移動元と移動先のサブスクリプションのテナント ID が同じでない場合、次の方法でテナント ID を調整する必要があります。

   * [Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 移動するリソースのリソース プロバイダーについて、移動先のサブスクリプションに登録する必要があります。 登録しないと、 **リソースの種類についてサブスクリプションへの登録が行われていない** ことを示すエラーが発生します。 このエラーは、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。

   PowerShell で登録状態を取得するには、次のコマンドを使用します。

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   リソース プロバイダーを登録するには、次のコマンドを使用します。

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI で登録状態を取得するには、次のコマンドを使用します。

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   リソース プロバイダーを登録するには、次のコマンドを使用します。

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. リソースを動かすアカウントには少なくとも次のアクセス許可を与える必要があります。

   * ソース リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**。
   * ターゲット リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/write**。

1. リソースを移動する前に、リソースの移動先となるサブスクリプションのサブスクリプション クォータをチェックします。 リソースを移動することでサブスクリプションが制限を上回る場合、クォータの引き上げを要求できるかどうかを確認する必要があります。 制限の一覧と引き上げを要求する方法については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

1. **サブスクリプション間で移動を行う場合は、リソースとその依存リソースを同じリソース グループ内に配置し、それらを一緒に移動する必要があります。** たとえば、マネージド ディスクを備えた VM では、マネージド ディスクを他の依存リソースと共に、VM と一緒に移動する必要があります。

   リソースを新しいサブスクリプションに移動する場合は、そのリソースに依存リソースがあるかどうか、およびそれらが同じリソース グループ内に置かれているかどうかを確認します。 それらのリソースが同じリソース グループ内にない場合は、同じリソース グループに統合することができるかどうかを確認します。 その場合は、リソース グループ間の移動操作を使用して、該当するすべてのリソースを同じリソース グループに配置します。

   詳細については、「[サブスクリプション間での移動のシナリオ](#scenario-for-move-across-subscriptions)」を参照してください。

## <a name="scenario-for-move-across-subscriptions"></a>サブスクリプション間での移動のシナリオ

サブスクリプション間でリソースを移動するには、次の 3 つの手順を行います。

![サブスクリプション間の移動のシナリオ](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

説明のため、依存リソースを 1 つだけ取り上げます。

* 手順 1:依存リソースが別々のリソース グループに分散されている場合は、まずそれらを 1 つのリソース グループに移動します。
* 手順 2:リソースと依存リソースを、ソース サブスクリプションからターゲット サブスクリプションにまとめて移動します。
* 手順 3:必要に応じて、依存リソースをターゲット サブスクリプション内の別々のリソース グループに再配布します。

## <a name="use-the-portal"></a>ポータルの使用

リソースを移動するには、それらのリソースが含まれているリソース グループを選択します。

移動するリソースを選択する。 すべてのリソースを移動するには、一覧の上部にあるチェック ボックスをオンにします。 または、リソースを個別に選択します。

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources-to-move.png" alt-text="リソースを選択":::

**[移動]** ボタンを選択します。

:::image type="content" source="./media/move-resource-group-and-subscription/select-move.png" alt-text="[移動] オプション":::

このボタンにより、次の 3 つのオプションが提示されます。

* 新しいリソース グループに移動します。
* 新しいサブスクリプションに移動します。
* 新しいリージョンに移動します。 リージョンを変更する方法については、「[リージョン間でのリソースの移動 (リソース グループから)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)」を参照してください。

リソースを新しいリソース グループに移動するか新しいサブスクリプションに移動するかを選択します。

ソース リソース グループが自動的に設定されます。 移動先のリソース グループを指定します。 新しいサブスクリプションに移行する場合は、サブスクリプションも指定します。 **[次へ]** を選択します。

:::image type="content" source="./media/move-resource-group-and-subscription/select-destination-group.png" alt-text="移動先のリソース グループを選択します":::

ポータルでは、リソースを移動できると検証されます。 検証の完了を待ちます。

:::image type="content" source="./media/move-resource-group-and-subscription/validation.png" alt-text="移動の検証":::

検証が正常に完了したら、 **[次へ]** を選択します。

そのリソースのツールとスクリプトを更新する必要があること確認します。 リソースの移動を開始するには、 **[移動]** を選択します。

:::image type="content" source="./media/move-resource-group-and-subscription/acknowledge-change.png" alt-text="移動先の選択":::

移動が完了すると、結果が通知されます。

:::image type="content" source="./media/move-resource-group-and-subscription/view-notification.png" alt-text="移動結果の表示":::

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

### <a name="validate"></a>検証

実際にリソースを移動せずに移動シナリオをテストするには、[AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) コマンドを使用します。 このコマンドは、結果を事前に確認する必要がある場合にのみ使用してください。 この操作を実行するには、次の要件を満たす必要があります。

* 移動元のリソース グループのリソース ID
* 移動先のリソース グループのリソース ID
* 移動する各リソースのリソース ID

```azurepowershell
Invoke-AzResourceAction -Action validateMoveResources `
-ResourceId "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" `
-Parameters @{ resources= @("/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}");targetResourceGroup = '/subscriptions/{subscription-id}/resourceGroups/{destination-rg}' }  
```

検証に成功した場合、出力は表示されません。

検証が失敗した場合は、リソースを移動できない理由を説明するエラー メッセージが表示されます。

### <a name="move"></a>詳細ビュー

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[Move-AzResource](/powershell/module/az.resources/move-azresource) コマンドを使用します。 次の例では、いくつかのリソースを新しいリソース グループに移動する方法を示します。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

新しいサブスクリプションに移動する場合は、`DestinationSubscriptionId` パラメーターの値を含めます。

## <a name="use-azure-cli"></a>Azure CLI の使用

### <a name="validate"></a>検証

実際にリソースを移動せずに移動シナリオをテストするには、[az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) コマンドを使用してください。 このコマンドは、結果を事前に確認する必要がある場合にのみ使用してください。 この操作を実行するには、次の要件を満たす必要があります。

* 移動元のリソース グループのリソース ID
* 移動先のリソース グループのリソース ID
* 移動する各リソースのリソース ID

要求本文で、`\"` を使用して二重引用符をエスケープします。

```azurecli
az resource invoke-action --action validateMoveResources \
  --ids "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" \
  --request-body "{  \"resources\": [\"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\"],\"targetResourceGroup\":\"/subscriptions/{subscription-id}/resourceGroups/{destination-rg}\" }" 
```

検証に成功した場合、以下のようになります。

```azurecli
{} Finished .. 
```

検証が失敗した場合は、リソースを移動できない理由を説明するエラー メッセージが表示されます。

### <a name="move"></a>詳細ビュー

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[az resource move](/cli/azure/resource#az_resource_move) コマンドを使用します。 移動するリソースのリソース ID を指定します。 次の例では、いくつかのリソースを新しいリソース グループに移動する方法を示します。 `--ids` パラメーターには、移動するリソース ID のスペース区切りリストを指定します。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

## <a name="use-rest-api"></a>REST API を使用する

### <a name="validate"></a>検証

[移動の検証操作](/rest/api/resources/resources/validate-move-resources)を使用すると、実際にリソースを移動することなく、必要な移動のシナリオをテストすることができます。 この操作は、正常に移動されるかどうかを確認する目的で使用します。 移動要求を送信すると、検証が自動的に呼び出されます。 この操作は、結果を事前に確認する必要がある場合にのみ使用してください。 この操作を実行するには、次の要件を満たす必要があります。

* 移動元のリソース グループの名前
* 移動先のリソース グループのリソース ID
* 移動する各リソースのリソース ID
* アカウントの[アクセス トークン](/rest/api/azure/#acquire-an-access-token)

次の要求を送信します。

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

要求本文:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

要求が正しい形式になっていれば、次の応答が操作から返されます。

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

状態コード 202 は、検証要求が受け付けられたものの、移動操作に成功するかどうかの判断はまだ出ていないことを示します。 `location` 値には、長時間実行される操作の状態をチェックするための URL が格納されます。

状態をチェックするには、次の要求を送信します。

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

操作が実行されている間は、継続的に状態コード 202 が返されます。 `retry-after` 値に示されている時間 (秒) が経過するのを待って再試行してください。 移動操作の検証が正常に完了すると、状態コード 204 が返されます。 移動の検証に失敗した場合は、次のようなエラー メッセージが返されます。

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

### <a name="move"></a>詳細ビュー

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[リソースの移動](/rest/api/resources/resources/moveresources)操作を使用します。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**質問: 通常は数分で完了するのですが、リソースの移動操作が、ほぼ 1 時間行われています。何か問題がありますか。**

リソースの移動は、さまざまなフェーズを持つ複雑な操作です。 移動しようとしているリソースのリソース プロバイダー以外のものが関与している可能性があります。 リソース プロバイダー間の依存関係を理由に、Azure Resource Manager では操作が完了するまで時間として 4 時間が許容されています。 この期間によって、リソース プロバイダーは一時的な問題から復旧する機会を与えられています。 移動要求が 4 時間以内である場合、操作は完了する試みを続けるため、まだ成功する可能性があります。 この期間中、移動元のリソース グループと移動先のリソース グループは一貫性の問題を回避するためにロックされます。

**質問: リソースの移動中に、リソース グループが 4 時間ロックされるのはなぜですか?**

移動要求の完了には、最大 4 時間の猶予が与えられます。 移動中のリソースに対する変更を防ぐために、リソースの移動中は、ソースとターゲットの両方のリソース グループがロックされます。

移動要求には 2 つのフェーズがあります。 最初のフェーズでは、リソースが移動されます。 2 番目のフェーズでは、移動中のリソースに依存している他のリソース プロバイダーに通知が送信されます。 リソース プロバイダーがいずれかのフェーズで失敗した場合、リソース グループが 4 時間にわたってロックされる可能性があります。 許容時間中、Resource Manager では失敗したステップが再試行されます。

4 時間の内にリソースを移動できなかった場合、Resource Manager によって両方のリソース グループのロックが解除されます。 正常に移動されたリソースは、移動先のリソース グループ内にあります。 移動に失敗したリソースは、移動元のリソース グループに残ります。

**質問: リソースの移動中に移動元のリソース グループと移動先のリソースグループがロックされると、どのような影響がありますか?**

このロックにより、いずれのリソース グループも削除できなくなり、いずれのリソース グループにも新しいリソースを作成できなくなり、移動に関与するリソースを削除することもできなくなります。

次の図は、進行中の移動の一部であるリソース グループをユーザーが削除しようとした場合に Azure portal から返されるエラー メッセージを示しています。

![削除を試みたときの移動エラー メッセージ](./media/move-resource-group-and-subscription/move-error-delete.png)

**質問: "MissingMoveDependentResources" というエラー コードはどういう意味ですか?**

リソースを移動する場合は、その依存リソースが、移動先のリソース グループまたはサブスクリプションに存在しているか、または移動要求に含まれている必要があります。 依存リソースがこの要件を満たしていない場合は、MissingMoveDependentResources エラー コードが返されます。 エラー メッセージには、移動要求に含める必要がある依存リソースに関する詳細が含まれています。

たとえば、仮想マシンを移動には、3 つの異なるリソース プロバイダーと、7 つのリソースの種類を移動することが必要な場合があります。 それらのリソース プロバイダーとリソースの種類を次に示します。

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

もう 1 つの一般的な例として、仮想ネットワークの移動があります。 その仮想ネットワークに関連付けられている他のいくつかのリソースを移動することが必要になる場合があります。 移動要求には、パブリック IP アドレス、ルート テーブル、仮想ネットワーク ゲートウェイ、ネットワーク セキュリティ グループなどの移動が必要になる場合があります。

**質問: エラー コード "RequestDisallowedByPolicy" は何を意味しますか?**

Resource Manager は、移動を試行する前に、移動要求を検証します。 この検証には、移動に関連するリソースで定義されているポリシーのチェックが含まれます。 たとえば、キー コンテナーを移動しようとしても、組織にターゲット リソース グループへのキー コンテナーの作成を拒否するポリシーがある場合、検証は失敗し、移動はブロックされます。 返されるエラー コードは **RequestDisallowedByPolicy です**。 

ポリシーの詳細については、[「Azureポリシーとは」](../../governance/policy/overview.md)を参照してください。

**質問: Azure 内の一部のリソースを移動できないのはなぜですか?**

現時点では、Azure 内のすべてのリソースが移動するわけではありません。 移動をサポートしているリソースのリストについては、「[リソースの移動操作のサポート」](move-support-resources.md)」を参照してください。

**質問: 1 回の操作で移動できるリソースの数はどのくらいですか?**

可能であれば、大規模な移動は個別の移動操作に分けます。 1 回の操作に含まれるリソースが 800 を超えると、Resource Manager から直ちにエラーが返されます。 ただし、800 未満のリソースの移動でも、タイムアウトで失敗になることがあります。

**質問: リソースが正常な状態にないというエラーの意味は何ですか?**

リソースが正常な状態にないために移動できないことを示すエラー メッセージが表示された場合、実際に移動をブロックしているのは依存リソースであることがあります。 通常、エラー コードは **MoveCannotProceedWithResourcesNotInSucceededState** です。

ソースまたはターゲットのリソース グループに仮想ネットワークが含まれている場合は、移動中に仮想ネットワークのすべての依存リソースの状態が確認されます。 この確認には、仮想ネットワークに直接的または間接的に依存するそのようなリソースが含まれます。 これらのリソースのいずれかがエラー状態の場合、移動はブロックされます。 たとえば、仮想ネットワークを使用する仮想マシンで障害が発生した場合、移動はブロックされます。 移動は、仮想マシンが移動するリソースの 1 つではなく、移動用のリソース グループのいずれにも存在しない場合でもブロックされます。

このエラーが発生した場合は、2 つの選択肢があります。 仮想ネットワークのないリソース グループにリソースを移動するか、[サポートにお問い合わせください](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>次のステップ

移動をサポートしているリソースのリストは、「[Move operation support for resources](move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。
