<properties
   pageTitle="開発環境のセットアップ | Microsoft Azure"
   description="ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。このセットアップを終えれば、アプリケーションを構築する準備は完了です。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/13/2016"
   ms.author="ryanwi"/>

# 開発環境を準備する

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 開発コンピューターで [Azure Service Fabric アプリケーション][1]をビルドして実行するには、ランタイム、SDK、ツールをインストールする必要があります。また、SDK に含まれる Windows PowerShell スクリプトの実行を有効にする必要があります。

## 前提条件
### サポートされるオペレーティング システムのバージョン
開発では、次のオペレーティング システムのバージョンがサポートされます。

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 には、既定では Windows PowerShell 2.0 のみが含まれます。Service Fabric PowerShell のコマンドレットには PowerShell 3.0 以降が必要です。Microsoft Download Center から [Windows PowerShell 5.0 をダウンロード][powershell5-download]できます。

## ランタイム、SDK、およびツールのインストール

Web Platform Installer には、Service Fabric 開発用に、次の 3 つの構成が用意されています。

- [Visual Studio 2015 のための Service Fabric ランタイム、SDK、ツールをインストールする (Visual Studio 2015 Update 2 以降が必要)][full-bundle-vs2015]
- [Service Fabric ランタイムと SDK のみをインストールする (Visual Studio Tools はインストールしない)][core-sdk]

## PowerShell スクリプトの実行の有効化

Service Fabric は、ローカル開発クラスターの作成、および Visual Studio からのアプリケーションのデプロイに、Windows PowerShell スクリプトを使用します。既定では、Windows はこれらのスクリプトの実行をブロックします。これらを有効にするには、PowerShell 実行ポリシーを変更する必要があります。管理者として PowerShell を開き、次のコマンドを入力します。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 次のステップ
開発環境のセットアップが完了したので、アプリのビルドと実行を開始してください。

- [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)
- [ローカル クラスター上でアプリケーションをデプロイし管理する方法](service-fabric-get-started-with-a-local-cluster.md)
- [サービスのフレームワークを選択する](service-fabric-choose-framework.md)
- [GitHub での Service Fabric コード サンプルの確認](https://aka.ms/servicefabricsamples)
- [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
- [Service Fabric のラーニング パスに沿ってプラットフォームの広範な概要を理解する](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric キャンペーン ページ"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI link"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]: https://www.microsoft.com/ja-JP/download/details.aspx?id=50395

<!---HONumber=AcomDC_0928_2016-->