---
title: クラウドに Kubernetes 開発環境を作成する | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 9bee5677aecb235872f50eea75ddc98bc453f426
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361717"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-net-core-and-visual-studio"></a>クイック スタート: Azure Dev Spaces (.NET Core および Visual Studio) を使用して Kubernetes 開発環境を作成する

このガイドでは、以下の方法について説明します。

- 開発用に最適化された Kubernetes ベースの環境を Azure に作成する。
- Visual Studio を使用して、コンテナー内のコードを繰り返し開発する。

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Visual Studio ツールを入手する 
1. 最新バージョンの [Visual Studio 2017](https://www.visualstudio.com/vs/) をインストールします。
1. Visual Studio インストーラーで、次のワークロードが選択されていることを確認します。
    * ASP.NET および Web の開発
1. [Azure Dev Spaces 用 Visual Studio 拡張機能](https://aka.ms/get-azds-visualstudio)をインストールします。

これで、Visual Studio を使用して ASP.NET Web アプリを作成する準備ができました。

## <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

Visual Studio 2017 で、新しいプロジェクトを作成します。 現時点では、このプロジェクトは **ASP.NET Core Web アプリケーション**である必要があります。 プロジェクトに "**webfrontend**" という名前を付けます。

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

**[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択し、ダイアログの上部にある 2 つのドロップダウンで **.NET Core** と **ASP.NET Core 2.0** が対象になっていることを確認します。 **[OK]** をクリックしてプロジェクトを作成します。

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Azure に開発環境を作成する

Azure Dev Spaces を使用すると、Azure によって完全に管理され、開発用に最適化された、Kubernetes ベースの開発環境を作成することができます。 先ほど作成したプロジェクトを開いた状態で、次の図のように、起動設定のドロップダウンから **[Azure Dev Spaces]** を選択します。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

次に表示されるダイアログで、適切なアカウントでサインインしていることを確認し、既存のクラスターを選択します。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

現時点では、**[スペース]** ドロップダウンは既定の `default` のままにしておきます。 このオプションについては、後で詳しく説明します。 パブリック エンドポイント経由で Web アプリにアクセスできるように、**[Publicly Accessible]\(パブリックにアクセス可能\)** チェック ボックスをオンにします。 この設定は必須ではありませんが、このチュートリアルの後の方でいくつかの概念を示す際に役立ちます。 いずれにせよ、Visual Studio を使用して Web サイトをデバッグできるので心配はありません。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

**[OK]** をクリックして、クラスターを選択または作成します。

Azure Dev Spaces と共に動作するように構成されていないクラスターを選択すると、クラスターを構成するかどうかをたずねるメッセージが表示されます。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**[OK]** を選択します。 

処理を完了するために、バックグラウンド タスクが開始されます。 完了するまでに、数分かかります。 まだ作成中かどうかを確認するには、次の画像に示すように、ステータス バーの左下隅にある **[バックグラウンド タスク]** アイコンをポイントします。

![](media/get-started-netcore-visualstudio/BackgroundTasks.png)

> [!Note]
開発環境が正常に作成されるまで、アプリケーションをデバッグすることはできません。

## <a name="look-at-the-files-added-to-project"></a>プロジェクトに追加されたファイルを確認する
開発環境が作成されるのを待っている間に、開発環境を使用することを選択したときにプロジェクトに追加されたファイルを確認しておきましょう。

まず、`charts` という名前のフォルダーが追加されており、このフォルダー内にアプリケーションの [Helm チャート](https://docs.helm.sh)がスキャフォールディングされていることがわかります。 これらのファイルは、アプリケーションを開発環境にデプロイするために使用されます。

`Dockerfile` という名前のファイルが追加されています。 このファイルには、標準の Docker 形式でアプリケーションをパッケージ化するために必要な情報が含まれています。 `HeaderPropagation.cs` ファイルも作成されています。このファイルについては、このチュートリアルで後ほど説明します。 

最後に、`azds.yaml` という名前のファイルがあります。このファイルには、開発環境に必要な構成情報 (パブリック エンドポイント経由でアプリケーションにアクセス可能かどうかなど) が含まれます。

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
開発環境が正常に作成されたら、アプリケーションをデバッグすることができます。 コードにブレークポイントを設定します。たとえば、`HomeController.cs` ファイルの 20 行目 (`Message` 変数が設定されている行) に設定します。 **F5** キーを押してデバッグを開始します。 

Visual Studio は環境開発と通信し、アプリケーションをビルドしてデプロイした後、ブラウザーを開いて Web アプリを実行します。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発環境で実行されています。 localhost アドレスであるのは、Azure Dev Spaces では Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されるためです。

ページの上部にある **[About]\(バージョン情報\)** リンクをクリックすると、ブレークポイントがトリガーされます。 コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](get-started-netcore-visualstudio.md#call-another-container)