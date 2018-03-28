---
title: Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する | Microsoft Docs
description: PowerShell を使用して Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: f56f00aabbef2cfa86264d3e962af9a9c0bafa98
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイックスタートでは、ポリシーの割り当てを作成して、管理ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、管理ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "*準拠していません*"。

Azure CLI は、コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このクイック スタートでは、CLI をローカルにインストールして使用するために、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。



## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシーの割り当てを作成し、"管理ディスクのない仮想マシンを監査" 定義を割り当てます。 このポリシー定義で、ポリシー定義に設定されている条件に準拠していないリソースを特定します。

ポリシーの割り当てを作成するには、次のコマンドを実行します。

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

上記のコマンドでは次の情報を使用します。

- **Name** - ポリシーの割り当てに使用する表示名。 このケースでは、"*Audit Virtual Machines without Managed Disks Assignment*" を使用します。
- **Policy** - 割り当ての作成に使用するポリシー定義 ID。 ここでは、"*Audit Virtual Machines without Managed Disks*" というポリシー定義です ポリシー定義 ID を取得するには、次のコマンドを実行します。`az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 &lt;scope&gt; は、実際のリソース グループの名前に置き換えてください。
- **Sku** - このコマンドでは、Standard レベルのポリシーの割り当てが作成されます。 Standard レベルでは、大規模な管理、コンプライアンス評価、修復が利用できます。 現在、Standard レベルは無料です。 将来は Standard レベルが有料となります。 料金の変更に関する告知と詳細は、「[Azure Policy の価格](https://azure.microsoft.com/pricing/details/azure-policy)」でお知らせいたします。


## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

この新しい割り当てに準拠していないリソースを表示するには、次のコマンドを実行してポリシー割り当て ID を取得します。

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

ポリシー割り当て ID の詳細については、「[Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)」を参照してください。

続けて、次のコマンドを実行して、非準拠リソースの ID を取得します。該当するリソース ID が JSON ファイルに出力されます。

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

次のような結果が返されます。

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}

```

この結果は、普段 Azure Portal ビューの **[準拠していないリソース]** に一覧表示される内容に相当します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のガイドは、このクイックスタートに基づいています。 引き続きチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、次のコマンドを実行して、作成した割り当てを削除してください。

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

ポリシーの割り当てについて詳しく学び、**今後**作成されるリソースが準拠していることを確認するには、次のチュートリアルに進んでしてください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./create-manage-policy.md)
