---
title: Service Fabric Mesh アプリをビルドするための Windows 開発環境の設定 | Microsoft Docs
description: Windows 開発環境を設定して、Service Fabric Mesh アプリケーションを作成し、Azure Service Fabric Mesh にデプロイします。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: bec0b9a7e34f1577f80a99f5380795c479c04bc8
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890468"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Service Fabric Mesh アプリをビルドするための Windows 開発環境の設定

Windows 開発マシンで Azure Service Fabric Mesh アプリケーションをビルドして実行するには、Service Fabric Mesh ランタイム、SDK、およびツールをインストールしてください。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>サポートされるオペレーティング システムのバージョン

開発では、次のオペレーティング システムのバージョンがサポートされます。

* Windows 10 (Enterprise、Professional、Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Service Fabric Mesh アプリケーションをデプロイするには、Visual Studio 2017 が必要です。 [バージョン 15.6.0][download-visual-studio] 以上をインストールし、次のワークロードを有効にします。

* ASP.NET および Web の開発
* Azure 開発

## <a name="install-docker"></a>Docker をインストールする

#### <a name="windows-10"></a>Windows 10

Service Fabric Mesh が使用するコンテナー化 Service Fabric アプリをサポートするために、最新バージョンの [Docker Community Edition for Windows][download-docker] をダウンロードしてインストールします。

インストール中に求められた場合は、**[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** を選択します。

マシン上で Hyper-V が有効になっていない場合、有効にするためのメッセージが Docker のインストーラーによって表示されます。 求められたら **[OK]** をクリックします。

#### <a name="windows-server-2016"></a>Windows Server 2016

Hyper-V ロールを有効にしていない場合は、管理者として PowerShell を開き、次のコマンドを実行して Hyper-V を有効にし、コンピューターを再起動します。 詳細については、[Docker Enterprise Edition for Windows Server][download-docker-server] に関するドキュメントを参照してください。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

コンピューターを再起動します。

管理者として PowerShell を開き、次のコマンドを実行して Docker をインストールします。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK とツール

Service Fabric Mesh ランタイム、SDK、ツールを次の順序でインストールします。

1. Web Platform Installer を使用して [Service Fabric Mesh SDK][download-sdkmesh] をインストールします。 Microsoft Azure Service Fabric の SDK とランタイムもインストールされます。
2. Visual Studio Marketplace から [Visual Studio Service Fabric Mesh Tools (プレビュー) の拡張機能][download-tools] をインストールします。

## <a name="build-a-cluster"></a>クラスターの構築

> [!IMPORTANT]
> Docker は、クラスターを構築する前に実行しておく**必要があります**。
> Docker が実行中であるかどうかをテストするには、ターミナル ウィンドウを開いて `docker ps` を実行し、エラーが発生するかどうか確認します。 応答でエラーが表示されない場合は、Docker は実行中になっており、クラスターを構築する準備ができています。

ローカル クラスターがない場合、Visual Studio は、ローカル クラスターを自動的に作成します。そのため、Visual Studio を使用している場合は、このセクションをスキップできます。

Service Fabric アプリを作成および実行する際のデバッグのパフォーマンスを最大限に高めるために、単一ノードのローカル開発クラスターの作成をお勧めします。 このクラスターは、Service Fabric Mesh プロジェクトをデプロイまたはデバッグする際は常に実行中の状態にしておく必要があります。

ランタイム、SDK、Visual Studio Tools、Docker をインストールし、Docker を実行したら、開発クラスターを作成します。

1. PowerShell ウィンドウを閉じます。
2. 管理者として、管理者特権で新しく PowerShell ウィンドウを開きます。 この手順は、最近インストールされた Service Fabric モジュールを読み込むために必要です。
3. 次の PowerShell コマンドを実行して、開発クラスターを作成します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. ローカルのクラスター マネージャー ツールを起動するには、次の PowerShell コマンドを実行します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. サービス クラスター マネージャー ツールが実行されたら (これはシステム トレイに表示されます)、それを右クリックし、**[Start Local Cluster]\(ローカル クラスターを起動する\)** をクリックします。

![図 1 - ローカル クラスターを起動する](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

これで、Service Fabric Mesh アプリケーションを作成する準備ができました。

## <a name="next-steps"></a>次の手順

[Azure Service Fabric アプリの作成](service-fabric-mesh-tutorial-create-dotnetcore.md)に関するチュートリアルを参照してください。

[よく寄せられる質問](service-fabric-mesh-faq.md)に対する回答を確認します。

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/