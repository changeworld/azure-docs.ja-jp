---
title: "Windows Server でのスタンドアロン Service Fabric クラスターのアップグレード | Microsoft Docs"
description: "スタンドアロン Service Fabric クラスターを実行している Service Fabric コード、構成、またはその両方をアップグレードします。クラスター アップグレード モードの設定などです。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 3d7e28c1cd221d704cf9cfec66da535e079fb472
ms.openlocfilehash: 30044abc0d7d42b11ddd210dfb9ea3eadb94dda6
ms.lasthandoff: 02/27/2017


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Windows Server でのスタンドアロン Service Fabric クラスターのアップグレード
> [!div class="op_single_selector"]
> * [Azure クラスター](service-fabric-cluster-upgrade.md)
> * [スタンドアロン クラスター](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

最新のシステムでは、アップグレード性を考慮した設計を行うことが、製品の長期的な成功を達成する鍵となります。 Azure Service Fabric クラスターは、ユーザーが所有するリソースの&1; つです。 この記事では、クラスターが Service Fabric のコードと構成を実行するとき、サポートされているバージョンを常に実行する方法について説明します。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>クラスター上で動作するファブリック バージョンの制御
Microsoft が Service Fabric 更新プログラムの新しいバージョンをリリースしたとき、クラスターがそれをダウンロードするように設定できます。あるいは、サポートされている Service Fabric バージョンを選択し、それをクラスターに適用できます。 

これは、"fabricClusterAutoupgradeEnabled" というクラスター構成を true または false に設定することで行います。

> [!NOTE]
> サポートされている Service Fabric バージョンを常にクラスターで実行するようにします。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、 [Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)でお知らせします。 その後間もなく新しいバージョンが利用できるようになります。 
> 
> 

各 Service Fabric ノードが別個の物理または仮想マシンに割り当てられる、運用スタイルのノード構成を使用している場合にのみ、クラスターを新しいバージョンにアップグレードできます。 1 台の物理または仮想マシンに複数の Service Fabric ノードが存在するような開発クラスターがある場合、クラスターを解体し、新しいバージョンで再構築する必要があります。

Service Fabric の最新バージョンまたはサポートされているバージョンにクラスターをアップグレードするとき、2 つの異なるワークフローがあります。 1 つは Service Fabric の最新バージョンを自動ダウンロードするための接続が与えられているクラスターのためのワークフローです。もう&1; つはそれが与えられていないクラスターのためのワークフローです。

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>最新のコードや構成をダウンロードするための接続が与えられているクラスターをアップグレードする
クラスター ノードが [http://download.microsoft.com](http://download.microsoft.com) にインターネット接続できる場合、以下の手順でクラスターをサポートされているバージョンにアップグレードします。 

[http://download.microsoft.com](http://download.microsoft.com) に接続できるクラスターに関しては、新しい Service Fabric バージョンが利用できるかどうか定期的に確認しています。

新しい Service Fabric バージョンが利用できるとき、パッケージがクラスターにローカル ダウンロードされ、アップグレードのためにプロビジョニングされます。 さらに、この新しいバージョンの情報をお客様に伝えるために、クラスターの正常性に関する次のような警告が表示されます。

“The current cluster version [version#] support ends [Date]." (現在のクラスター バージョン [バージョン番号] のサポートが終了します [日付])。クラスターで最新バージョンが実行されると、警告が消えます。

#### <a name="cluster-upgrade-workflow"></a>クラスター アップグレード ワークフロー。
クラスターの正常性に関する警告が表示されたら、次の手順を実行する必要があります。

1. クラスターにノードとして列挙されているすべてのマシンに管理者アクセスできるマシンからクラスターに接続します。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。
   
    ```powershell
   
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
2. アップグレードできる Service Fabric バージョンの一覧を取得します。
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    次のような出力が表示されます。
   
    ![ファブリックのバージョンを取得する][getfabversions]
3. [Start-ServiceFabricClusterUpgrade PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx) を使用し、入手可能なバージョンの&1; つにクラスターをアップグレードします。
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Service Fabric Explorer を使用するか、次の PowerShell コマンドを実行すれば、アップグレードの進捗状況を監視できます。
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 Start-ServiceFabricClusterUpgrade コマンドの実行時に正常性のカスタム ポリシーを指定できます。詳しくは、[このドキュメント](https://msdn.microsoft.com/library/mt125872.aspx)を参照してください。 

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>最新のコードや構成をダウンロードするための<U>接続が与えられていない</u>クラスターをアップグレードする
クラスター ノードが [http://download.microsoft.com](http://download.microsoft.com) にインターネット接続**できない**場合、以下の手順でクラスターをサポートされているバージョンにアップグレードします。 

> [!NOTE]
> 実行しているクラスターがインターネットに接続されていない場合、新しいリリースの情報は Service Fabric チームのブログで確認する必要があります。 クラスターの正常性に関する警告は表示**されません**。  
> 
> 

1. クラスター構成を変更し、次のプロパティを false に設定します。
   
        "fabricClusterAutoupgradeEnabled": false,

構成アップグレードを開始します。 使用方法の詳細については、[Start-ServiceFabricClusterConfigurationUpgrade PS cmd](https://msdn.microsoft.com/en-us/library/mt788302.aspx)を参照してください。 構成アップグレードを始める前に、JSON の 'clusterConfigurationVersion' を更新してください。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>クラスター アップグレード ワークフロー。
1. [Create service fabric cluster for windows server](service-fabric-cluster-creation-for-windows-server.md) (Windows Server の Service Fabric クラスターを作成する) ドキュメントから最新バージョンのパッケージをダウンロードします。
2. クラスターにノードとして列挙されているすべてのマシンに管理者アクセスできるマシンからクラスターに接続します。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。 
   
    ```powershell
   
    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. ダウンロードしたパッケージをクラスター イメージ ストアにコピーします。
   
    ```powershell
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. コピーしたパッケージを登録します。 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
5. 入手できるバージョンの&1; つにクラスターをアップグレードします。 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Service Fabric Explorer を使用するか、次の PowerShell コマンドを実行すれば、アップグレードの進捗状況を監視できます。
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 Start-ServiceFabricClusterUpgrade コマンドの実行時に正常性のカスタム ポリシーを指定できます。詳しくは、[このドキュメント](https://msdn.microsoft.com/library/mt125872.aspx)を参照してください。 

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。


## <a name="cluster-configuration-upgrade"></a>クラスター構成のアップグレード
クラスター構成のアップグレードを実行するには、Start-ServiceFabricClusterConfigurationUpgrade を実行します。 構成のアップグレードは、アップグレード ドメインごとに処理されます。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```


## <a name="next-steps"></a>次のステップ
* [Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-up-down.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

