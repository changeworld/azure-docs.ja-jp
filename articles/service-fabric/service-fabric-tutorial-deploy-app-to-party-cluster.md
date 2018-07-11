---
title: Azure で Service Fabric アプリをクラスターにデプロイする | Microsoft Docs
description: Visual Studio でアプリケーションをクラスターにデプロイする方法を説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f83ebcce68a7abe53d7b8eaeff5913a907e3df9a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344191"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>チュートリアル: Azure のクラスターに Service Fabric アプリケーションをデプロイする

このチュートリアルはシリーズの第 2 部です。ここでは、Visual Studio で直接 Azure Service Fabric アプリケーションを Azure の新しいクラスターにデプロイする方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio でクラスターを作成する
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * アプリケーションをリモート クラスターにデプロイする
> * [ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Visual Studio Team Services を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。 [Service Fabric クラスター](/service-fabric/service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンであり、マイクロサービスがデプロイおよび管理されます

Visual Studio 内のデプロイには 2 つのオプションがあります。

* Visual Studio から Azure 内にクラスターを作成します。 このオプションでは、お好きな構成を使用して、セキュリティで保護されたクラスターを Visual Studio で直接作成できます。 この種類のクラスターはテスト シナリオに最適です。Visual Studio 内でクラスターを作成して、それに対して直接発行できます。
* サブスクリプションで既存のクラスターに発行します。  [Azure Portal](https://portal.azure.com) 経由で、[PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) または [Azure CLI](./scripts/cli-create-cluster.md) スクリプトを使用するか、または [Azure Resource Manager テンプレート](service-fabric-tutorial-create-vnet-and-windows-cluster.md)から Service Fabric クラスターを作成することができます。

このチュートリアルでは、Visual Studio からクラスターを作成します。 既にクラスターがデプロイされている場合は、接続エンドポイントをコピーして貼り付けるか、サブスクリプションからそれを選択できます。
> [!NOTE]
> 多くのサービスは、リバース プロキシを使用して相互に通信します。 Visual Studio で作成されたクラスターとパーティ クラスターでは、既定でリバース プロキシが有効です。  既存のクラスターを使用する場合、[クラスターでリバース プロキシを有効にする](service-fabric-reverseproxy.md#setup-and-configuration)必要があります。

### <a name="find-the-votingweb-service-endpoint"></a>VotingWeb サービス エンドポイントを見つける

まず、フロントエンド Web サービスのエンドポイントを見つけます。  フロントエンド Web サービスは、特定のポートでリッスンしています。  Azure でアプリケーションがクラスターにデプロイされると、クラスターとアプリケーションの両方が Azure ロード バランサーの背後で実行します。  受信トラフィックが Web サービスを通過できるように、Azure ロード バランサーでアプリケーション ポートが開かれている必要があります。  ポート (8080 など) は、*VotingWeb/PackageRoot/ServiceManifest.xml* ファイルの **Endpoint** 要素にあります。

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

次の手順で、**[クラスターの作成]** ダイアログの **[詳細]** タブにこのポートを指定します。  既存のクラスターにアプリケーションをデプロイする場合は、[PowerShell スクリプト](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)を使用するか、または [Azure Portal](https://portal.azure.com) から Azure ロード バランサーでこのポートを開くことができます。

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Visual Studio から Azure にクラスターを作成する

ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックし、**[発行]** を選択します。

サブスクリプションにアクセスできるように、Azure アカウントを使用してサインインします。 パーティ クラスターを使用する場合、この手順はオプションです。

**[接続のエンドポイント]** ドロップダウンを選択して、**<Create New Cluster...>** オプションを選択します。

![[発行] ダイアログ](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

**[クラスターの作成]** ダイアログで、次のように設定を変更します。

1. **[クラスター名]** フィールドでクラスターの名前を指定します。また、使用するサブスクリプションと場所を指定します。
2. 省略可能: ノードの数を変更できます。 既定では、Service Fabric のシナリオをテストするのに最低限必要な 3 ノードになっています。
3. **[証明書]** タブを選択します。このタブでは、クラスターの証明書をセキュリティで保護するために使用されるパスワードを入力します。 この証明書は、クラスターのセキュリティ保護に役立ちます。 また、証明書を保存したい場所にパスを変更することもできます。 アプリケーションをクラスターに発行するうえで必要な手順であるため、Visual Studio では証明書を自動でインポートすることもできます。
4. **[VM の詳細]** タブを選択します。クラスターを構成する仮想マシン (VM) に使用したいパスワードを指定します。 ユーザー名とパスワードは、VM へのリモート接続に使用できます。 また、VM マシン サイズを選択できるほか、必要に応じて VM イメージを変更できます。
5. **[詳細設定]** タブで、クラスターと同時に作成される Azure ロード バランサーで開かせるポートの一覧を変更できます。  前の手順で検出された VotingWeb サービス エンドポイントを追加します。 さらに、アプリケーション ログ ファイルをルーティングする既存の Application Insights キーを追加することもできます。
6. 設定の変更が完了したら、**[作成]** ボタンを選択します。 作成の完了には数分かかります。クラスターが完全に作成されると、出力ウィンドウにそのことが示されます。

![[クラスターの作成] ダイアログ](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>サンプル アプリケーションをデプロイする

使用するクラスターの準備が整ったら、アプリケーション プロジェクトを右クリックし、**[発行]** を選択します。

発行が完了した後は、ブラウザーからアプリケーションに要求を送信できます。

好みのブラウザーを開き、クラスター アドレス (ポート情報を除いた接続エンドポイント。たとえば、win1kw5649s.westus.cloudapp.azure.com) を入力します。

アプリケーションをローカルで実行するときに確認したのと同じ結果が表示されます。

![クラスターからの API 応答](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio でクラスターを作成する
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [HTTPS を有効にする](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
