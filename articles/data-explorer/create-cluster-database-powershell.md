---
title: クイック スタート:PowerShell を使用して Azure Data Explorer クラスターとデータベースを作成する
description: PowerShell を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287383"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>PowerShell を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


このクイック スタートでは、PowerShell を使用して、Azure Data Explorer クラスターとデータベースを作成する方法について説明します。

Windows、Linux、または [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) で PowerShell コマンドレットとスクリプトを実行して、[Azure Data Explorer](https://docs.microsoft.com/azure/kusto/ ) を作成および構成できます。

[**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )。 Azure PowerShell と **Az.Kusto** を使用すると、次のタスクを実行できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="configure-parameters"></a>パラメーターを構成する

Azure Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカル環境で CLI を実行している場合、次の手順に従って Azure にサインインし、現在のサブスクリプションを設定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. クラスターを作成するサブスクリプションを設定します。

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Az.Kusto モジュールをお使いのデバイスにインストールします。
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | Name | *mykustocluster* | クラスターの任意の名前。|
   | Sku | *D13_v2* | クラスターに使用される SKU。 |
   | ResourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。

2. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

結果に値が `Succeeded` の `provisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | ClusterName | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | Name | *mykustodatabase* | データベースの名前。|
   | ResourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | SoftDeletePeriod | *3650:00:00:00* | データをクエリに使用できるようにしておく時間。 |
   | HotCachePeriod | *3650:00:00:00* | データをキャッシュに保持する時間。 |

2. 次のコマンドを実行して、作成したデータベースを確認します。

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>次の手順

他の Az.Kusto コマンドは[**こちら**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )で確認できます。

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](net-standard-ingest-data.md)
