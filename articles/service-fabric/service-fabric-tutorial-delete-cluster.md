---
title: Azure で Service Fabric クラスターを削除する | Microsoft Docs
description: このチュートリアルでは、Azure でホストされている Service Fabric クラスターとそのすべてのリソースを削除する方法について説明します。 クラスターを含むリソース グループを削除するか、リソースを選択して削除することができます。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 70c5fa5de627b69623b1cce6929615f4e99e2a05
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410823"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>チュートリアル: Azure で実行されている Service Fabric クラスターを削除する

このチュートリアルはシリーズの第 4 部です。Azure で実行されている Azure Service Fabric クラスターを削除する方法について説明します。 Service Fabric クラスターを完全に削除するには、そのクラスターによって使用されているリソースも削除する必要があります。 オプションは 2 つあります。クラスターのあるリソース グループを削除 (クラスター リソースとリソース グループ内のその他すべてのリソースが削除されます) するか、クラスター リソースとその関連するリソース (リソース グループ内のその他のリソースは対象から除かれます) を個別に削除します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループとそのすべてのリソースを削除する
> * リソース グループからリソースを選択して削除する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して、セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * クラスターの削除

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Azure PowerShell モジュールのバージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)または [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成します。

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Service Fabric クラスターを含むリソース グループを削除する
クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットまたは [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) コマンドを使用します。

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>クラスター リソースと関連リソースを選択して削除する
リソース グループに、削除する Service Fabric クラスターに関連付けられているリソースのみがある場合は、リソース グループ全体を削除する方が簡単です。 リソース グループのリソースを選択して削除し、クラスターに関連付けられていないリソースを維持する場合、次の手順に従います。

リソース グループのリソースを一覧表示します。

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzureRmResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

削除するリソースごとに、次のスクリプトを実行します。

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

クラスター リソースを削除するには、次のスクリプトを実行します。

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * リソース グループとそのすべてのリソースを削除する
> * リソース グループからリソースを選択して削除する

このチュートリアルはこれで完了です。次は以下の説明をお読みください。
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用して Service Fabric クラスターを検査し、管理する方法。
* クラスター ノードの [Windows オペレーティング システム](service-fabric-patch-orchestration-application.md)または [Linux オペレーティング システムにパッチを適用する](service-fabric-patch-orchestration-application-linux.md)方法。
* [Windows クラスター](service-fabric-diagnostics-event-aggregation-wad.md)または [Linux クラスター](service-fabric-diagnostics-event-aggregation-lad.md)のイベントを集計する方法とクラスター イベントを監視するように [Log Analytics を設定する](service-fabric-diagnostics-oms-setup.md)方法。