---
title: Azure で Service Fabric ランタイムをアップグレードする | Microsoft Docs
description: このチュートリアルでは、PowerShell を使用して、Azure でホストされる Service Fabric クラスターのランタイムをアップグレードする方法を説明します。
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 367528353d5849a64ed1924d1b1e02a9b2c38252
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007935"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>チュートリアル: Azure で Service Fabric クラスターのランタイムをアップグレードする

このチュートリアルは、3 つのシリーズの一部で、Azure Service Fabric クラスター上の Service Fabric ランタイムをアップグレードする方法について説明します。 チュートリアルのこの部分は、Azure で実行されている Service Fabric クラスター用に書かれており、スタンドアロン Service Fabric クラスターには適用されません。

> [!WARNING]
> チュートリアルのこの部分では、PowerShell が必要です。 クラスターのランタイムをアップグレードする機能は、Azure CLI ツールでまだサポートされていません。 代替方法として、クラスターはポータルからアップグレードできます。 詳細については、「[Azure Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade.md)」をご覧ください。

クラスターで既に最新の Service Fabric ランタイムが実行されている場合は、この手順を実行する必要はありません。 ただ、この記事は、サポート対象のランタイムを Azure Service Fabric クラスターにインストールする場合も使用できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * クラスター バージョンの読み取り
> * クラスター バージョンの設定

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して、セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * クラスターのランタイムをアップグレードする
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Azure PowerShell モジュールのバージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)または [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成します。
* Windows クラスターをデプロイする場合は、Windows 開発環境を設定します。 [Visual Studio 2017](http://www.visualstudio.com)、**Azure 開発**ワークロード、**ASP.NET および Web 開発**ワークロード、**.NET Core クロス プラットフォーム開発**ワークロードをインストールします。  その後、[.NET 開発環境](service-fabric-get-started.md)をセットアップします。
* Linux クラスターをデプロイする場合は、Java 開発環境を [Linux](service-fabric-get-started-linux.md) または [MacOS](service-fabric-get-started-mac.md) にセットアップします。  [Service Fabric CLI](service-fabric-cli.md) をインストールします。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>ランタイム バージョンの取得

Azure に接続したら、Service Fabric クラスターを含むサブスクリプションを選択して、クラスターのランタイムのバージョンを取得できます。

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

または、次の手順で、サブスクリプション内のすべてのクラスターの一覧を取得します。

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

**ClusterCodeVersion** 値に注目してください。 この値は次のセクションで使用します。

## <a name="upgrade-the-runtime"></a>ランタイムをアップグレードする

前のセクションの **ClusterCodeVersion** の値を `Get-ServiceFabricRuntimeUpgradeVersion` コマンドレットで使用して、アップグレードに使用可能なバージョンを検出します。 このコマンドレットは、インターネットに接続されているコンピューターからのみ実行できます。 たとえば、バージョン `5.7.198.9494` からアップグレードすることが可能なランタイム バージョンを表示するには、次のコマンドを使用します。

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

バージョンのリストを使用して、新しいランタイムにアップグレードする Azure Service Fabric クラスターを判断できます。 たとえば、バージョン `6.0.219.9494` がアップグレードできる場合、次のコマンドを使用して、クラスターをアップグレードします。

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> クラスターのアップグレードが完了するまでには時間がかかる場合があります。 アップグレードの実行中は PowerShell がブロックされます。 別の PowerShell セッションを使用して、アップグレードの状態を確認できます。

アップグレードの状態は、PowerShell または Azure Service Fabric CLI (sfctl) を使用して監視できます。

まず、チュートリアルの最初の部分で作成した SSL 証明書を使用して、クラスターに接続します。 `Connect-ServiceFabricCluster` コマンドレット、または `sfctl cluster upgrade-status` を使用します。

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

次に、`Get-ServiceFabricClusterUpgrade` または `sfctl cluster upgrade-status` を使用して状態を表示します。 結果は次のようになります。

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * クラスターのランタイムのバージョンを取得する
> * クラスター ランタイムをのアップグレードする
> * アップグレードを監視する

次のチュートリアルでは、Service Fabric クラスターを使用して API Management をデプロイする方法について説明します。
> [!div class="nextstepaction"]
> [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)
