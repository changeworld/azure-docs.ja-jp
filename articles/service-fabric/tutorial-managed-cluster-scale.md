---
title: Service Fabric マネージド クラスターをスケールアウトする (プレビュー)
description: このチュートリアルでは、Service Fabric マネージド クラスターのノード タイプをスケールアウトする方法について説明します。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: a6777743932f3cc73db973c4d9935d4b5827700b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410210"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>チュートリアル:Service Fabric マネージド クラスターをスケールアウトする (プレビュー)

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

* Service Fabric マネージド クラスター ("[*マネージド クラスターのデプロイ*](tutorial-managed-cluster-deploy.md)" に関するページを参照)。
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) 以降 (「[*Azure PowerShell のインストール*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true)」を参照)。

## <a name="scale-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターをスケーリングする
スケーリングしたいノード タイプのノード数を増減させるには、インスタンス数を変更します。 ノード タイプの名前は、クラスター デプロイの Azure Resource Manager テンプレート (ARM テンプレート) または Service Fabric Explorer で確認できます。  

> [!NOTE]
> ノード タイプがプライマリである場合、ノード数を 3 未満 (Basic SKU クラスターの場合) や 5 未満 (Standard SKU クラスターの場合) にすることはできません。

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
