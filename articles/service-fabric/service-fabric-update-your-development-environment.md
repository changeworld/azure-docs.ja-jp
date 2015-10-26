<properties
   pageTitle="Service Fabric 開発環境の更新 | Microsoft Azure"
   description="Service Fabric 開発環境を更新して、最新のランタイム、SDK、およびツールを使用します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# Service Fabric 開発環境の更新

 Service Fabric は定期的に、ローカルでの開発に使用するランタイム、SDK、ツールの新しいリリースを提供します。これらのリリースでローカル開発環境を常に更新しておくと、アプリケーションをローカルで構築してテストする際に、いつでも最新の機能とバグの修正を利用し、パフォーマンスを高めることができます。

## ローカル クラスターのクリーンアップ

 現在 Service Fabric では、ローカル クラスターの実行中には、Service Fabric ランタイムのアップグレードをサポートしていません。クリーンなアップグレードを実行するには、まず、ローカル クラスターをクリーンアップすることが重要です。

 >[AZURE.NOTE]ローカル クラスターをクリーンアップすると、デプロイ済みのすべてのアプリケーションとそのデータが削除されます。

 次のように、ローカル クラスターをクリーンアップできます。


 1. その他のすべての PowerShell ウィンドウを閉じて、管理者として新しいウィンドウを起動します。

 2. `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"` を使用してクラスター セットアップ ディレクトリに移動します。

 3. `.\CleanCluster.ps1` を実行します。


## ランタイム、SDK、およびツールの更新

 既存のクラスターを正常にクリーンアップした後に、次のようにアップグレードを続行できます。


 1. Web Platform Installer を起動して、[新しいリリースに更新][1]します。

 2. 完了したら、管理者として新しい PowerShell ウィンドウを起動し、`cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"` で、クラスターのセットアップ ディレクトリに移動します。

 3. `.\DevClusterSetup.ps1` を実行し、ローカル クラスターをセットアップします。

これで完了です。 Visual Studio を起動し、Service Fabric アプリケーションの構築を続行できます。

>[AZURE.NOTE]既定のプロジェクトの構造は、このリリースで変更されました。既存のプロジェクトは、Visual Studio で開いて実行することができます。ただし、アプリケーションのビルド、配置、またはデバッグに問題が発生した場合は、新しいプロジェクトを作成して、コードを移行することを検討してください。

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI のリンク"

<!---HONumber=Oct15_HO3-->