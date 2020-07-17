---
title: 'Kubernetes 上でデバッグと反復処理を行う: Visual Studio と .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: このクイックスタートでは、Azure Dev Spaces と Visual Studio を使用し、Azure Kubernetes Service 上で .NET Core アプリケーションのデバッグと迅速な反復型開発を行う方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 1aa2545f3bd4e7558c99a31dca43f65510bab59e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872133"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>クイック スタート:Kubernetes 上でデバッグと反復処理を行う: Visual Studio と .NET Core - Azure Dev Spaces

このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- Visual Studio を使用して、コンテナー内のコードを繰り返し開発する。
- Visual Studio を使用して、クラスターで実行されるコードをデバッグする。

Azure Dev Spaces では、次のものを使用してデバッグと反復処理を行うこともできます。
- [Java と Visual Studio Code](quickstart-java.md)
- [Node.js と Visual Studio Code](quickstart-nodejs.md)
- [.NET Core と Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free)ことができます。
- Azure 開発ワークロードがインストールされている Windows 上の Visual Studio 2019。 Visual Studio をインストールしていない場合は、[ここ](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)からダウンロードします。

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成する

[サポートされているリージョン][supported-regions]で AKS クラスターを作成する必要があります。 クラスターを作成するには:

1. [Azure ポータル](https://portal.azure.com)
1. *+ [リソースの作成]、[Kubernetes サービス]* の順に選択します。 
1. _[サブスクリプション]_ 、 _[リソース グループ]_ 、 _[Kubernetes クラスター名]_ 、 _[リージョン]_ 、 _[Kubernetes バージョン]_ 、 _[DNS 名のプレフィックス]_ の入力を行います。

    ![Azure portal での AKS の作成](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. *[Review + create]\(レビュー + 作成\)* をクリックします。
1. *Create* をクリックしてください。

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS クラスターで Azure Dev Spaces を有効にする

Azure portal でご自分の AKS クラスターに移動して、 *[Dev Spaces]* をクリックします。 *[Dev Spaces の使用]* を *[はい]* に変更して *[保存]* をクリックします。

![Azure portal での Dev Spaces の有効化](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>新しい ASP.NET Web アプリを作成する

1. Visual Studio を開きます。
1. 新しいプロジェクトを作成します。
1. *[ASP.NET Core Web アプリケーション]* を選択し、 *[次へ]* をクリックします。
1. プロジェクト *webfrontend* に名前を付けて、 *[作成]* をクリックします。
1. 要求されたら、 *[Web アプリケーション (モデル ビュー コントローラー)]* をテンプレートに選択します。
1. 上部にある *[.NET Core]* と *[ASP.NET Core 2.1]* を選択します。
1. *Create* をクリックしてください。

## <a name="connect-your-project-to-your-dev-space"></a>プロジェクトを開発空間に接続する

ご自分のプロジェクトで、次の図のように、起動設定のドロップダウンから **[Azure Dev Spaces]** を選択します。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

[Azure Dev Spaces] ダイアログで、ご自分の "*サブスクリプション*" と "*Azure Kubernetes クラスター*" を選択します。 *[空間]* は *[default]* に設定しておいて、 *[公的にアクセス可能]* チェック ボックスをオンします。 [*OK*] をクリックします。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

このプロセスによって、パブリックにアクセスできる URL を備えた *default* 開発空間にお客様のサービスがデプロイされます。 Azure Dev Spaces と共に動作するように構成されていないクラスターを選択すると、クラスターを構成するかどうかをたずねるメッセージが表示されます。 [*OK*] をクリックします。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

*default* 開発空間で稼働するサービスのパブリック URL が、 *[出力]* ウィンドウに表示されます。

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

上記の例では、パブリック URL は `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` です。 

**[デバッグ]** を選択し、次に **[デバッグの開始]** を選択します。 数秒後にサービスが開始され、Visual Studio でサービスのパブリック URL を使用してブラウザーが開かれます。 ブラウザーが自動的に開かれない場合は、ブラウザーでご自分のサービスのパブリック URL に移動し、開発空間で実行されているサービスを操作します。

このプロセスにより、サービスへのパブリック アクセスが無効になった可能性があります。 パブリック アクセスを有効にするには、[*values.yaml* 内のイングレス値][ingress-update]を更新することができます。

## <a name="update-code"></a>コードの更新

Visual Studio がまだ開発空間に接続されている場合は、停止ボタンをクリックします。 `Controllers/HomeController.cs` の 20 行目を以下に変更します。
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

変更を保存し、 **[デバッグ]** を選択してから、 **[デバッグの開始]** を選択します。 数秒後にサービスが開始され、Visual Studio でサービスのパブリック URL を使用してブラウザーが開かれます。 ブラウザーが自動的に開かれない場合は、ブラウザーでサービスのパブリック URL に移動し、 *[概要]* をクリックします。 更新されたメッセージが表示されることを確認します。

Azure Dev Spaces では、コードが編集されるたびに新しいコンテナー イメージをリビルドして再デプロイするのではなく、既存のコンテナー内でコードの増分再コンパイルを実行して、編集とデバッグのループを高速化します。

## <a name="setting-and-using-breakpoints-for-debugging"></a>デバッグ用のブレークポイントを設定して使用する

Visual Studio がまだ開発空間に接続されている場合は、停止ボタンをクリックします。 `Controllers/HomeController.cs` を開いて、20 行目のどこかをクリックし、そこにカーソルを置きます。 ブレークポイントを設定するには、*F9* キーを押すか、 *[デバッグ]* 、 *[ブレークポイントの設定/解除]* の順にクリックします。 開発空間においてデバッグ モードでサービスを開始するには、*F5* キーを押すか、または *[デバッグ]* 、 *[デバッグの開始]* の順にクリックします。

ブラウザーでサービスを開き、メッセージが表示されないことに注目します。 Visual Studio に戻って、20 行目が強調表示されていることを確認します。 設定したブレークポイントによって、20 行目でサービスが一時停止されました。 サービスを再開するには、*F5* キーを押すか、 *[デバッグ]* 、 *[続行]* の順にクリックします。 ブラウザーに戻って、メッセージが表示されたことに注目します。

デバッガーがアタッチされた状態で Kubernetes でサービスを稼働している間、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

`Controllers/HomeController.cs` の 20 行目にカーソルを置いて *F9* キーを押すことで、ブレークポイントを削除します。

## <a name="clean-up-your-azure-resources"></a>Azure リソースをクリーンアップする

Azure portal でご自分のリソース グループに移動して、 *[リソース グループの削除]* をクリックします。 または、[az aks delete](/cli/azure/aks#az-aks-delete) コマンドを使用できます。

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
