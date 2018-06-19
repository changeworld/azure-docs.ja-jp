---
title: クラウドに Kubernetes 開発空間を作成する | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824640"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>クイック スタート: Azure Dev Spaces を使用して Kubernetes 開発環境を作成する (.NET Core および Visual Studio)

このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- Visual Studio を使用して、コンテナー内のコードを繰り返し開発する。
- クラスターで実行されるコードをデバッグする。

> [!Note]
> **問題が発生した場合は**いつでも、「[トラブルシューティング](troubleshooting.md)」セクションを参照するか、このページでコメントを投稿してください。 より詳細な[チュートリアル](get-started-netcore-visualstudio.md)を試すこともできます。

## <a name="prerequisites"></a>前提条件

- 米国東部、西ヨーロッパ、またはカナダ東部の各リージョンで Kubernetes 1.9.6 を実行中の Kubernetes クラスターで、[Http アプリケーションのルーティング] が有効になっているクラスター。

  ![[Http アプリケーションのルーティング] が有効であることを確認してください。](media/common/Kubernetes-Create-Cluster-3.PNG)

- Web 開発ワークロードがインストールされている Visual Studio 2017。 インストールしていない場合は、[ここから](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)ダウンロードします。

## <a name="set-up-azure-dev-spaces"></a>Azure Dev Spaces をセットアップする

[Azure Dev Spaces 用 Visual Studio 拡張機能](https://aka.ms/get-azds-visualstudio)をインストールします。

## <a name="connect-to-a-cluster"></a>クラスターへの接続

次に、Azure Dev Spaces 用のプロジェクトを作成して構成します。

### <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

Visual Studio 2017 で、新しいプロジェクトを作成します。 現時点では、このプロジェクトは **ASP.NET Core Web アプリケーション**である必要があります。 プロジェクトに **webfrontend** という名前を付けます。

**[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択し、**.NET Core** と **ASP.NET Core 2.0** が対象になっていることを確認します。

### <a name="create-a-dev-space-in-azure"></a>Azure に Dev Space を作成する

先ほど作成したプロジェクトを開いた状態で、次の図のように、起動設定のドロップダウンから **[Azure Dev Spaces]** を選択します。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

次に表示されるダイアログで、適切なアカウントでサインインしていることを確認し、既存のクラスターを選択します。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

現時点では、**[スペース]** ドロップダウンは `default` のままにしておきます。 パブリック エンドポイント経由で Web アプリにアクセスできるように、**[Publicly Accessible]\(パブリックにアクセス可能\)** チェック ボックスをオンにします。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

**[OK]** をクリックして、クラスターを選択または作成します。

Azure Dev Spaces と共に動作するように構成されていないクラスターを選択すると、クラスターを構成するかどうかをたずねるメッセージが表示されます。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**[OK]** を選択します。 

### <a name="look-at-the-files-added-to-project"></a>プロジェクトに追加されたファイルを確認する
開発空間が作成されるのを待っている間に、Azure Dev Spaces を使用することを選択したときにプロジェクトに追加されたファイルを確認しておきましょう。

- `charts` という名前のフォルダーが追加されています。このフォルダー内にアプリケーションの [Helm チャート](https://docs.helm.sh)がスキャフォールディングされています。 これらのファイルは、アプリケーションを開発空間に展開するために使用されます。
- `Dockerfile` には、標準の Docker 形式でアプリケーションをパッケージ化するために必要な情報が含まれています。
- `azds.yaml` には、開発空間で必要な構成情報 (パブリック エンドポイント経由でアプリケーションにアクセス可能かどうかなど) が含まれています。

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
開発空間が正常に作成されたら、アプリケーションをデバッグすることができます。 コードにブレークポイントを設定します。たとえば、`HomeController.cs` ファイルの 20 行目 (`Message` 変数が設定されている行) に設定します。 **F5** キーを押してデバッグを開始します。 

Visual Studio は開発空間と通信してアプリケーションをビルドして展開した後、ブラウザーを開いて Web アプリを実行します。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。 localhost アドレスであるのは、Azure Dev Spaces では Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されるためです。

ページの上部にある **[About]\(バージョン情報\)** リンクをクリックすると、ブレークポイントがトリガーされます。 コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](get-started-netcore-visualstudio.md#call-another-container)