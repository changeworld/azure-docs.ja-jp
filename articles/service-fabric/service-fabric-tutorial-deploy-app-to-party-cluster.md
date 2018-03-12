---
title: "Visual Studio で Azure Service Fabric アプリケーションをクラスターにデプロイする | Microsoft Docs"
description: "Visual Studio でアプリケーションをクラスターにデプロイする方法を説明します"
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2018
ms.author: mikkelhegn
ms.custom: mvc
ms.openlocfilehash: 21c991a4e3f9ae19a4ad4a96427fdc1c91c55a1c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>チュートリアル: Azure の Service Fabric クラスターにアプリケーションをデプロイする
このチュートリアルはシリーズの第 2 部です。ここでは、Visual Studio で直接 Azure Service Fabric アプリケーションを Azure の新しいクラスターにデプロイする方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio でクラスターを作成する
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする


このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * アプリケーションをリモート クラスターにデプロイする
> * [Visual Studio Team Services を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする
[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>サンプル アプリケーションをデプロイする

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>発行先の Service Fabric クラスターを選択する
これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。

デプロイには 2 つのオプションがあります。
- Visual Studio でクラスターを作成します。 このオプションでは、お好きな構成を使用して、セキュリティで保護されたクラスターを Visual Studio で直接作成できます。 この種類のクラスターはテスト シナリオに最適です。Visual Studio 内でクラスターを作成して、それに対して直接発行できます。
- サブスクリプションで既存のクラスターに発行します。

このチュートリアルでは、Visual Studio でクラスターを作成する手順を実行します。 その他のオプションについては、接続エンドポイントをコピーして貼り付けるか、サブスクリプションから選択できます。
> [!NOTE]
> 多くのサービスは、リバース プロキシを使用して相互に通信します。 Visual Studio で作成されたクラスターとパーティ クラスターでは、既定でリバース プロキシが有効です。  既存のクラスターを使用する場合、[クラスターでリバース プロキシを有効にする](service-fabric-reverseproxy.md#setup-and-configuration)必要があります。

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>アプリを Service Fabric クラスターにデプロイする

1. ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックし、**[発行]** を選択します。

2. サブスクリプションにアクセスできるように、Azure アカウントを使用してサインインします。 パーティ クラスターを使用する場合、この手順はオプションです。

3. **[接続のエンドポイント]** ドロップダウンを選択して、"<Create New Cluster...>" オプションを選択します。
    
    ![[発行] ダイアログ](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. [クラスターの作成] ダイアログで、次の設定を変更します。

    1. [クラスター名] フィールドでクラスターの名前を変更します。また、使用したいサブスクリプションと場所を指定します。
    2. 省略可能: ノードの数を変更できます。 既定では、Service Fabric のシナリオをテストするのに最低限必要な 3 ノードになっています。
    3. [証明書] タブを選択します。このタブでは、クラスターの証明書をセキュリティで保護するために使用されるパスワードを入力します。 この証明書は、クラスターのセキュリティ保護に役立ちます。 また、証明書を保存したい場所にパスを変更することもできます。 アプリケーションをクラスターに発行するうえで必要な手順であるため、Visual Studio では証明書を自動でインポートすることもできます。
    4. [VM の詳細] タブを選択します。クラスターを構成する仮想マシン (VM) に使用したいパスワードを指定します。 ユーザー名とパスワードは、VM へのリモート接続に使用できます。 また、VM マシン サイズを選択できるほか、必要に応じて VM イメージを変更できます。
    5. 省略可能: [詳細設定] タブでは、クラスターと同時に作成されるロード バランサーでオープンにするポートの一覧を変更できます。 さらに、アプリケーション ログ ファイルのルーティングに使用される既存の Application Insights キーを追加することもできます。
    6. 設定の変更が完了したら、[作成] ボタンを選択します。 作成の完了には数分かかります。クラスターが完全に作成されると、出力ウィンドウにそのことが示されます。
    
    ![[クラスターの作成] ダイアログ](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. 使用するクラスターの準備が整ったら、アプリケーション プロジェクトを右クリックし、**[発行]** を選択します。

    発行が完了した後は、ブラウザーからアプリケーションに要求を送信できます。

5. 好みのブラウザーを開き、クラスター アドレス (ポート情報を除いた接続エンドポイント。たとえば、win1kw5649s.westus.cloudapp.azure.com) を入力します。

    アプリケーションをローカルで実行するときに確認したのと同じ結果が表示されます。

    ![クラスターからの API 応答](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio でクラスターを作成する
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Visual Studio Team Services を使用して継続的インテグレーションを設定する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
