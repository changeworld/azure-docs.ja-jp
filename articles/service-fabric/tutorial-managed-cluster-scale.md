---
title: Service Fabric マネージド クラスターをスケールアウトする
description: このチュートリアルでは、Service Fabric マネージド クラスターのノード タイプをスケールアウトする方法について説明します。
ms.topic: tutorial
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 938c163f4b27706999f7fe32f7935d4d7877c96b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864972"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster"></a>チュートリアル: Service Fabric マネージド クラスターをスケールアウトする

このチュートリアル シリーズでは、次のことについて説明します。

> [!div class="checklist"]
> * [Service Fabric マネージド クラスターをデプロイする方法](tutorial-managed-cluster-deploy.md)
> * Service Fabric マネージド クラスターをスケールアウトする方法
> * [Service Fabric マネージド クラスターのノード タイプを追加および削除する方法](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric マネージド クラスターにアプリケーションをデプロイする方法](tutorial-managed-cluster-deploy-app.md)

シリーズのこの部分では、次を行う方法について説明します。

> [!div class="checklist"]
> * Service Fabric マネージド クラスター ノードをスケーリングする

## <a name="prerequisites"></a>前提条件

* Service Fabric マネージド クラスター ([*マネージド クラスターのデプロイ*](tutorial-managed-cluster-deploy.md) に関するページを参照)。
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) 以降 (「[*Azure PowerShell のインストール*](/powershell/azure/install-az-ps)」を参照)。

## <a name="scale-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターをスケーリングする
スケーリングしたいノード タイプのノード数を増減させるには、インスタンス数を変更します。 ノード タイプの名前は、クラスター デプロイの Azure Resource Manager テンプレート (ARM テンプレート) または Service Fabric Explorer で確認できます。  

> [!NOTE]
> プライマリ ノード タイプの場合、ノード数を 3 未満 (Basic SKU クラスターの場合) や 5 未満 (Standard SKU クラスターの場合) にすることはできません。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

クラスターのアップグレードが自動的に開始され、数分後に新しいノードが表示されます。

## <a name="next-steps"></a>次の手順

この手順では、Service Fabric マネージド クラスターのノード タイプをスケーリングしました。 ノード タイプの追加と削除について詳しくは、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターのノード タイプを追加および削除する](tutorial-managed-cluster-add-remove-node-type.md)
