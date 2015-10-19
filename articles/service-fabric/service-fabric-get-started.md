<properties
   pageTitle="Service Fabric 開発環境の設定 | Microsoft Azure"
   description="Service Fabric ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/07/2015"
   ms.author="seanmck"/>

# Service Fabric 開発環境の設定
 この記事では、ランタイム、SDK、ツールのインストール、およびローカル クラスターの設定など、[Service Fabric][1] アプリのビルド開始に必要な事柄すべてを説明します。

 >[AZURE.NOTE]次の手順は、新しい PC を設定するためのものです。PC に以前のバージョンの Service Fabric がインストールされている場合は、[開発環境の更新手順](service-fabric-update-your-development-environment.md)に従ってください。

## 前提条件
### サポートされるオペレーティング システムのバージョン
次のオペレーティング システムのバージョンがサポートされます。

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Service Fabric のツールは Visual Studio 2015 に依存しています。Visual Studio 2015 は、[ここ][2]から入手できます。

> [AZURE.NOTE]サポートされる OS のバージョンを実行していないか、PC に Visual Studio 2015 をインストールしたくない場合は、VM ギャラリーからのイメージを使用して Windows Server 2012 R2 と Visual Studio 2015 がプレインストールされている Azure 仮想マシンを設定できます。

## ランタイム、SDK、およびツールのインストール

Service Fabric のコンポーネントは、Web Platform Installer によってインストールされます。次の手順に従ってインストールします。

1. Web Platform Installer を使用して [SDK をダウンロード][3]します。

2. **[インストール]** をクリックして、インストール プロセスを開始します。

3. 使用許諾契約書を確認し、同意します。

自動的にインストールが続行します。

## PowerShell スクリプトの実行の有効化

Service Fabric は、ローカル開発クラスターの作成、および Visual Studio からのアプリケーションのデプロイに、Windows PowerShell スクリプトを使用します。既定では、Windows はこれらのスクリプトの実行をブロックします。これらを有効にするには、PowerShell 実行ポリシーを変更する必要があります。管理者として PowerShell を開き、次のコマンドを入力します。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## ローカル クラスターをインストールして開始します。
ローカル クラスターは、最終的に 1 台の開発用コンピューター上で稼働中に使用する複数のコンピューターのトポロジを表わしています。ローカル クラスターをセットアップするには次の手順に従います。


1. その他のすべての PowerShell ウィンドウを閉じて、管理者として新しいウィンドウを起動します。

2. クラスター セットアップ ディレクトリに移動します。

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```

3. 実行

    ```powershell
    .\DevClusterSetup.ps1
    ```

すぐにノード情報と、クラスターが正常に作成されたことを示す出力が表示されます。Service Fabric ホスト サービスとネーム サービスの起動中に警告が表示されることがあります。これは正常なことであり、すぐにクラスターに関する基本的な情報が表示されます。

> [AZURE.NOTE]ローカル クラスターは、Azure で実行されるものとまったく同じランタイムを使用します。シミュレートやエミュレートは実行されません。唯一の違いは、Azure の場合のようにノードが複数のコンピューターに分配されるのではなく、すべてのノードが 1 台のコンピューターで実行されることです。

## クラスター セットアップの検証

SDK に付属している Service Fabric エクスプローラー ツールを使用して、クラスターが正常に作成されたことを確認できます。

1. 次のコマンドを実行して、Service Fabric Explorer を起動します。

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. 左上隅の [Onebox/ローカル クラスター] ノードを展開します。

3. アプリケーションとノードのビューが緑になっていることを確認します。

緑ではない要素があるか、エラーが表示されている場合は、しばらく待ってから [更新] ボタンをクリックします。引き続き問題がある場合は、「[セットアップのトラブルシューティング手順](service-fabric-troubleshoot-local-cluster-setup.md)」を確認してください。

## 次のステップ
開発環境の設定が完了しました。これで、アプリのビルドと実行を開始できます。

- [プログラミング モデルについての詳細: 高信頼アクターおよび高信頼サービス](service-fabric-choose-framework.md)
- [高信頼サービス API の使用](service-fabric-reliable-services-quick-start.md)
- [高信頼アクター API の使用](service-fabric-reliable-actors-get-started.md)
- [GitHub での Service Fabric サンプルの確認](https://github.com/azure/servicefabric-samples)
- [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric キャンペーン ページ"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI のリンク"

<!---HONumber=Oct15_HO2-->