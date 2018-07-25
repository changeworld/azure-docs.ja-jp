---
title: Service Fabric mesh アプリの Windows 開発環境の設定 | Microsoft Docs
description: Windows 開発環境を設定して、Service Fabric mesh アプリケーションを作成し、Azure Service Fabric mesh にデプロイします。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125163"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Windows 開発環境のセットアップと Service Fabric アプリケーションのビルド

Windows 開発マシンで Azure Service Fabric アプリケーションをビルドして実行するには、Service Fabric のランタイム、SDK、およびツールをインストールしてください。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>サポートされるオペレーティング システムのバージョン

開発では、次のオペレーティング システムのバージョンがサポートされます。

* Windows 10 (Enterprise、Professional、Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Hyper-V の有効化

Service Fabric アプリを作成するには、Hyper-V を有効にする必要があります。 

### <a name="windows-10"></a>Windows 10

管理者として PowerShell を開き、次のコマンドを実行します。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

コンピューターを再起動します。 HYPER-V を有効にする方法の詳細については、「[Windows 10 上に Hyper-V をインストールする](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)」を参照してください。

### <a name="windows-server-2016"></a>Windows Server 2016

管理者として PowerShell を開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

コンピューターを再起動します。 HYPER-V を有効にする方法の詳細については、「[Install the Hyper-V role on Windows Server 2016 (Windows Server 2016 上に Hyper-V のロールをインストールする)](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)」を参照してください。

## <a name="visual-studio"></a>Visual Studio

Service Fabric アプリをデプロイするには、Visual Studio 2017 が必要です。 [バージョン 15.6.0][download-visual-studio] 以上をインストールし、次のワークロードを有効にします。

- ASP.NET および Web の開発
- Azure 開発

## <a name="docker"></a>Docker

Service Fabric mesh が使用するコンテナー化された Service Fabric アプリをサポートするため、Docker をインストールします。

### <a name="windows-10"></a>Windows 10

最新バージョンの [Docker Community Edition for Windows][download-docker] をダウンロードしてインストールします。 

インストール中に求められた場合は、**[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** を選択します。 その後、サインアウトし、もう一度サインインします。 もう一度サインインした後、それまでに Hyper-V を有効にしていなかった場合は、Hyper-V の有効化を求めるメッセージが表示されることがあります。 Hyper-V を有効にしてから、コンピューターを再起動する必要があります。

コンピューターが再起動すると、Docker から**コンテナー**機能を有効にするよう求められます。有効にして、コンピューターを再起動します。

### <a name="windows-server-2016"></a>Windows Server 2016

次の PowerShell コマンドを使用して Docker をインストールします。 詳細については、[Docker Enterprise Edition for Windows Server][download-docker-server] に関するドキュメントを参照してください。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

コンピューターを再起動します。

## <a name="sdk-and-tools"></a>SDK とツール

Service Fabric mesh ランタイム、SDK、ツールを依存関係の順序に従ってインストールします。

1. Web Platform Installer を使用して [Service Fabric mesh SDK][download-sdkmesh] をインストールします。 Microsoft Azure Service Fabric の SDK とランタイムもインストールされます。
2. Visual Studio Marketplace から [Visual Studio Service Fabric Tools (プレビュー) の拡張機能][download-tools] をインストールします。

## <a name="build-a-cluster"></a>クラスターの構築

Service Fabric アプリを作成および実行する際のデバッグのパフォーマンスを最大限に高めるために、単一ノードのローカル開発クラスターの作成をお勧めします。 このクラスターは、Service Fabric mesh プロジェクトをデプロイまたはデバッグする際は常に実行中の状態にしておく必要があります。

Docker は、クラスターを構築する前に実行しておく**必要があります**。 Docker が実行中であるかどうかをテストするには、ターミナル ウィンドウを開いて `docker ps` を実行し、エラーが発生するかどうか確認します。 応答でエラーが表示されない場合は、Docker は実行中になっており、クラスターを構築する準備ができています。

ランタイム、SDK、Visual Studio Tools をインストールしたら、開発クラスターを作成します。

1. PowerShell ウィンドウを閉じます。
2. 管理者として、管理者特権で新しく PowerShell ウィンドウを開きます。 この手順は、インストールした Service Fabric モジュールを読み込むために必要です。
3. 次の PowerShell コマンドを実行して、開発クラスターを作成します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. ローカルのクラスター マネージャー ツールを起動するには、次の PowerShell コマンドを実行します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

これで、Service Fabric mesh アプリケーションを作成する準備ができました。

## <a name="next-steps"></a>次の手順

[Azure Service Fabric アプリの作成](service-fabric-mesh-tutorial-create-dotnetcore.md)に関するチュートリアルを参照してください。

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
