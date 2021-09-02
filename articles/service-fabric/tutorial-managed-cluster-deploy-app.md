---
title: PowerShell を使用して Service Fabric マネージド クラスターにアプリケーションをデプロイする
description: このチュートリアルでは、Service Fabric マネージド クラスターに接続し、PowerShell を使用してアプリケーションをデプロイします。
ms.topic: tutorial
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b1228deaf5627c8a367a9b0927320f93d98a50e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867442"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster"></a>チュートリアル: Service Fabric マネージド クラスターにアプリケーションをデプロイする

このチュートリアル シリーズでは、次のことについて説明します。

> [!div class="checklist"]
> * [Service Fabric マネージド クラスターをデプロイする方法](tutorial-managed-cluster-deploy.md)
> * [Service Fabric マネージド クラスターをスケールアウトする方法](tutorial-managed-cluster-scale.md)
> * [Service Fabric マネージド クラスターにノードを追加および削除する方法](tutorial-managed-cluster-add-remove-node-type.md)
> * Service Fabric マネージド クラスターにアプリケーションをデプロイする方法

シリーズのこの部分では、次を行う方法について説明します。

> [!div class="checklist"]
> * Service Fabric マネージド クラスターに接続する
> * クラスターにアプリケーションをアップロードする
> * クラスターでのアプリケーションのインスタンスを作成する
> * クラスターからアプリケーションを削除する

## <a name="prerequisites"></a>前提条件

* Service Fabric マネージド クラスター ([*マネージド クラスターのデプロイ*](tutorial-managed-cluster-deploy.md) に関するページを参照)。

## <a name="connect-to-your-cluster"></a>クラスターへの接続

クラスターに接続するには、クラスター証明書の拇印が必要になります。 この値は、リソース デプロイのクラスター プロパティの出力で確認するか、既存リソースのクラスター プロパティにクエリを実行することで確認できます。

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

### <a name="upload-an-application-package"></a>アプリケーション パッケージのアップロード

このチュートリアルでは、[Service Fabric 投票アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy)のサンプルを使用します。 PowerShell を使用した Service Fabric アプリケーションのデプロイの詳細については、[Service Fabric でのアプリケーションのデプロイと削除](service-fabric-deploy-remove-applications.md)に関する記事を参照してください。

> [!NOTE]
> Service Fabric マネージド クラスターのプレビューでは、Visual Studio から直接アプリケーションを発行することはできません。

まず、[デプロイ用にアプリケーションをパッケージ化](service-fabric-package-apps.md)する必要があります。 このチュートリアルでは、Visual Studio 内からアプリケーションをパッケージ化するための手順に従ってください。 下のパスで使用するため、アプリケーションがパッケージ化される場所のパスを忘れずにメモしておいてください。

アプリケーション パッケージを作成したら、アプリケーション パッケージをクラスターにアップロードできます。 アプリケーション パッケージが存在する場所のパスを表すように `$path` の値を更新し、以下を実行します。

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>アプリケーションの作成

New-ServiceFabricApplication コマンドレットでは、正常に登録されているアプリケーションの種類のバージョンを指定して、アプリケーションをインスタンス化することができます。 各アプリケーションの名前は、 "fabric:" スキームで開始され、各アプリケーション インスタンスに対して一意でなければなりません。 ターゲット アプリケーションの種類のアプリケーション マニフェストに定義されている既定のサービスも作成されます。

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

この操作が完了すると、Service Fabric Explorer でアプリケーション インスタンスが実行されていることを確認できます。

### <a name="remove-an-application"></a>アプリケーションの削除

アプリケーション インスタンスが不要になった場合は、`Remove-ServiceFabricApplication` コマンドレットで名前を指定して完全に削除できます。これにより、そのアプリケーションに属するすべてのサービスも自動的に削除され、すべてのサービスの状態が完全に削除されます。

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>次の手順

この手順では、Service Fabric マネージド クラスターにアプリケーションをデプロイしました。 アプリケーションのデプロイ オプションの詳細については、次を参照してください。

* [管理対象クラスターにアプリケーション シークレットをデプロイする](how-to-managed-cluster-application-secrets.md)
* [Azure Resource Manager を使用してアプリケーションを管理対象クラスターにデプロイする](how-to-managed-cluster-app-deployment-template.md)
* [マネージド ID を使用してアプリケーションを管理対象クラスターにデプロイする](how-to-managed-cluster-application-managed-identity.md)


マネージド クラスターの構成オプションの詳細については、次を参照してください。

* [マネージド クラスターを構成する](how-to-managed-cluster-configuration.md)
