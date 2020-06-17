---
title: クイック スタート - PowerShell を使用して Azure Databricks ワークスペースを作成する
description: このクイック スタートでは、PowerShell を使用して Azure Databricks ワークスペースを作成する方法を説明します。
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485690"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>クイック スタート:PowerShell を使用して Azure Databricks ワークスペースを作成する

このクイック スタートでは、PowerShell を使用して Azure Databricks ワークスペースを作成する方法について説明します。 PowerShell を使用して、対話形式またはスクリプトで Azure リソースを作成および管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> Az.Databricks PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にインストールする必要があります: `Install-Module -Name Az.Databricks -AllowPrerelease`。
> Az.Databricks PowerShell モジュールが一般提供されると、将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

Azure Databricks を初めて使用する場合は、**Microsoft.Databricks** リソースプロバイダーを登録する必要があります。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプション ID を選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、**myResourceGroup** という名前のリソース グループを**米国西部 2** リージョンに作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、PowerShell を使用して Azure Databricks ワークスペースを作成します。

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

次の値を指定します。

|       **プロパティ**       |                                                                                **説明**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 名前                     | Databricks ワークスペースの名前を指定します                                                                                                                                   |
| ResourceGroupName        | 既存のリソース グループ名を指定します                                                                                                                                        |
| 場所                 | **[米国西部 2]** を選択します。 その他の利用可能なリージョンについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)に関するページをご覧ください                                     |
| ManagedResourceGroupName | 新しい管理対象リソース グループを作成するか、既存のグループを使用するかを指定します。                                                                                        |
| Sku                      | **Standard**、**Premium**、**Trial** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格](https://azure.microsoft.com/pricing/details/databricks/)に関するページをご覧ください |

ワークスペースの作成には数分かかります。 この処理が完了すると、ユーザー アカウントが管理者ユーザーとして自動的にワークスペースに追加されます。

ワークスペースのデプロイが失敗した場合でも、ワークスペースはエラー状態で作成されます。 失敗したワークスペースを削除し、デプロイ エラーのない新しいワークスペースを作成します。 失敗したワークスペースを削除すると、管理対象リソース グループと、正常にデプロイされたリソースもすべて削除されます。

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Databricks ワークスペースのプロビジョニングの状態を確認する

Databricks ワークスペースが正常にプロビジョニングされたかどうかを確認するには、`Get-AzDatabricksWorkspace` コマンドレットを使用できます。

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースが別のクイックスタートまたはチュートリアルで必要でない場合は、次の例を実行して削除できます。

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこのクイックスタートの範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

リソース グループを削除せずに、このクイックスタートで作成したサーバーのみを削除するには、`Remove-AzDatabricksWorkspace` コマンドレットを使用します。

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Databricks に Spark クラスターを作成する](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
