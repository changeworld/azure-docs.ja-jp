---
title: Service Fabric マネージド クラスターに接続する
description: Service Fabric マネージド クラスターに接続する方法について説明します
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aa168a283be909678a199eee887d69bb3eb2024a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089746"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターに接続する

[ポータル、Azure Resource Manager テンプレート](quickstart-managed-cluster-template.md)、または[PowerShell](tutorial-managed-cluster-deploy.md) を使用してクラスターをデプロイしたら、さまざまな方法でマネージド クラスターに接続して表示できます。 

## <a name="use-azure-portal"></a>Azure Portal の使用

マネージド クラスター リソースに移動するには

 1) https://portal.azure.com/ にアクセスします

 2) Service Fabric を検索し、[Service Fabric マネージド クラスター] を選択して、クラスター リソースに移動します

 3) 自分のクラスターを選択する

 4) この方法では、特定のパラメーターを表示および変更できます。 詳細については、使用可能な[クラスター構成オプション](how-to-managed-cluster-configuration.md)を参照してください。

## <a name="use-service-fabric-explorer"></a>Service Fabric Explorer の使用

[Service Fabric Explorer](https://github.com/Microsoft/service-fabric-explorer) (SFX) は、Microsoft Azure Service Fabric クラスターのアプリケーションとクラスターの正常性を検査および管理するためのアプリケーションです。 

マネージド クラスターの SFX に移動するには
 
 1) https://portal.azure.com/ にアクセスします
 
 2) Service Fabric を検索し、[Service Fabric マネージド クラスター] を選択して、クラスター リソースに移動します

 3) 自分のクラスターを選択する

 4) 右上にある `SF Explorer` を見つけます。次に例を示します。 https://mycluster.region.cloudapp.azure.com:19080/Explorer

## <a name="use-powershell-modules"></a>PowerShell モジュールを使用する

次の PowerShell モジュールを使用して、クラスターの接続、表示、構成の変更を行うことができます。 

* [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールします。

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (以降) をインストールします。

### <a name="using-the-service-fabric-powershell-module"></a>Service Fabric PowerShell モジュールの使用
このモジュールを使用するには、クラスター証明書の拇印が必要です。 この値は、リソース デプロイのクラスター プロパティの出力で確認するか、既存リソースのクラスター プロパティにクエリを実行することで確認できます。

次のコマンドは、クラスター証明書の拇印をクラスター リソースに照会するために使用できます。

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprints
```

クラスター証明書の拇印があれば、クラスターに接続することができます。

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="using-the-azure-service-fabric-powershell-module"></a>Azure Service Fabric PowerShell モジュールの使用

Azure Service Fabric モジュールを使用すると、マネージド クラスターの作成、ノード タイプのスケーリング、マネージド クラスター リソース情報の表示などの操作を実行できます。 マネージド クラスターでサポートされている特定のコマンドレットには、[Az. ServiceFabric PowerShell モジュール](/powershell/module/az.servicefabric/) のドキュメントで参照できる `AzServiceFabricManagedCluster*` という名前が付いています。


次の例では、いずれかのコマンドレットを使用して、マネージド クラスターの詳細を表示します。

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>次の手順

* [Azure Resource Manager を使用してアプリケーションを管理対象クラスターにデプロイする](how-to-managed-cluster-app-deployment-template.md)
* [マネージド クラスター オプションを構成する](how-to-managed-cluster-configuration.md)



