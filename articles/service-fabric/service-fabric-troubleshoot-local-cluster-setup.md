<properties
   pageTitle="ローカル クラスターのセットアップをトラブルシューティングする"
   description="この記事には、ローカル開発クラスターのトラブルシューティングに関する推奨事項が記載されています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="seanmck"/>

# ローカル開発クラスターのセットアップをトラブルシューティングする

ローカル開発クラスターとの対話中に問題が発生した場合は、次の推奨事項を確認して解決の参考にしてください。

## クラスターのセットアップに関するエラー

### Service Fabric のログをクリーンアップできない

#### 問題点

DevClusterSetup スクリプトの実行中に、次のようなエラーが発生します。

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### 解決策

現在開いている Powershell ウィンドウを閉じ、新しい Powershell ウィンドウを管理者として開きます。これでスクリプトを正常に実行できるようになります。

## クラスターの接続に関するエラー

### 型の初期化で例外が発生する

#### 問題点

PowerShell または Service Fabric Explorer でクラスターに接続する際に、System.Fabric.Common.AppTrace で TypeInitializationException が発生します。

#### 解決策

インストール時に Path 変数が正しく設定されませんでした。Windows をログオフして再度ログインしてください。これでパスが最新の状態に更新されます。

### "object is closed" のエラーが発生してクラスターの接続が失敗する

#### 問題点

次のようなエラーが発生して、Connect-ServiceFabricCluster の呼び出しが失敗します。

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### 解決策

現在開いている Powershell ウィンドウを閉じ、新しい Powershell ウィンドウを管理者として開きます。これで正常に接続できるようになります。

### FabricConnectionDeniedException

#### 問題点

Visual Studio を使用してデバッグしている際に、FabricConnectionDeniedException が発生します。

#### 解決策

このエラーは通常、ランタイムサービス ホスト プロセスを Service Fabric からではなく手動で開始すると発生します。

お使いのソリューションでサービス プロジェクトがスタートアップ プロジェクトに設定されていないことを確認します。スタートアップ プロジェクトに設定できるプロジェクトは、Service Fabric アプリケーションのプロジェクトのみです。


## 次のステップ

- [システム正常性レポートでクラスターを理解してトラブルシューティングする](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

<!---HONumber=August15_HO6-->