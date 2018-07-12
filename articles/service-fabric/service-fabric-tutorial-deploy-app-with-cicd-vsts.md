---
title: Azure で継続的インテグレーションを使用して Service Fabric アプリをデプロイする (Team Services) | Microsoft Docs
description: このチュートリアルでは、Visual Studio Team Services を使用して、継続的インテグレーションと Service Fabric アプリケーションのデプロイをセットアップする方法について説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f3cc4f518278cca915e40bd691c6a7674219916e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109394"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>チュートリアル: CI/CD を使用して Service Fabric クラスターへアプリケーションをデプロイする

このチュートリアルはシリーズの第 4 部です。Visual Studio Team Services を使用して、Azure Service Fabric アプリケーションの継続的インテグレーションとデプロイを設定する方法について説明します。  既存の Service Fabric アプリケーションが必要で、[.NET アプリケーション ビルド](service-fabric-tutorial-create-dotnet-app.md)で作成されたアプリケーションを例として使用します。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * Team Services でビルド定義を作成する
> * Team Services でリリース定義を作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Visual Studio Team Services を使用して CI/CD を構成する
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。
* [このチュートリアルに従って](service-fabric-tutorial-create-vnet-and-windows-cluster.md)、たとえば、Azure 上に Windows Service Fabric クラスターを作成します。
* [Team Services アカウント](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) を作成します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>発行プロファイルの準備

ここまでで、[アプリケーションを作成し](service-fabric-tutorial-create-dotnet-app.md)、[アプリケーションを Azure にデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)しました。これで、継続的インテグレーションをセットアップする準備ができました。  まず、Team Services 内で実行されるデプロイ プロセスで使用する発行プロファイルを、アプリケーション内に準備します。  発行プロファイルは、あらかじめ作成したクラスターを対象とするように構成する必要があります。  Visual Studio を起動し、既存の Service Fabric アプリケーション プロジェクトを開きます。  **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[発行]** を選択します。

継続的インテグレーション ワークフローに使用するアプリケーション プロジェクト内で目標一覧表 (たとえば Cloud) を選択します。  クラスター接続エンドポイントを指定します。  Team Services 内の各デプロイ用にアプリケーションがアップグレードされるよう、**[アプリケーションのアップグレード]** チェックボックスをオンにします。  **[保存]** ハイパーリンクをクリックして設定を発行プロファイルに保存し、**[キャンセル]** をクリックしてダイアログ ボックスを閉じます。

![プロファイルのプッシュ][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>新しい Team Services の Git リポジトリで Visual Studio ソリューションを共有する

ビルドを生成できるよう、Team Services のチーム プロジェクトで、アプリケーションのソース ファイルを共有します。

Visual Studio の右下隅のステータス バーにある **[ソース管理** ->  **Git に追加]** を選択して、プロジェクトの新しいローカル Git リポジトリを作成します。

**チーム エクスプローラー**の **[プッシュ]** ビューで、**[Visual Studio Team Services へのプッシュ]** の下にある **[Git リポジトリの発行**] ボタンを選択します。

![Git リポジトリのプッシュ][push-git-repo]

電子メールを確認し、**[Team Services ドメイン]** ドロップダウンから自分のアカウントを選択します。 リポジトリ名を入力し、**[リポジトリの発行]** を選択します。

![Git リポジトリのプッシュ][publish-code]

リポジトリを公開すると、ローカル リポジトリと同じ名前の新しいチーム プロジェクトが自分のアカウントに作成されます。 既存のチーム プロジェクトでリポジトリを作成するには、**[リポジトリ]** 名の横にある **[詳細]** をクリックして、チーム プロジェクトを選択します。 **[See it on the web]\(Web を参照\)** を選択すると、Web でコードを表示できます。

## <a name="configure-continuous-delivery-with-vsts"></a>VSTS を利用した継続的配信を設定する

Team Services のビルド定義では、順次実行される一連のビルド ステップで構成されたワークフローを記述します。 Service Fabric クラスターをデプロイするため、Service Fabric アプリケーション パッケージおよび他のアーティファクトを生成するビルド定義を作成します。 Team Services のビルド定義の詳細については、[こちら](https://www.visualstudio.com/docs/build/define/create)をご覧ください。 

Team Services のリリース定義では、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します ビルド定義とリリース定義を併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Team Services のリリース定義の詳細については、 [こちら](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)をご覧ください。

### <a name="create-a-build-definition"></a>ビルド定義の作成

Web ブラウザーを開き、新しいチーム プロジェクト ([https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting)) に移動します。

**[ビルドとリリース]** タブ、**[ビルド]**、**[+ 新しい定義]** の順に選択します。  **[テンプレートの選択]** で **[Azure Service Fabric アプリケーション]** テンプレートを選択し、**[適用]** をクリックします。

![ビルド テンプレートを選択する][select-build-template]

**[タスク]** の **[エージェント キュー]** に「Hosted VS2017」と入力します。

![タスクを選択する][save-and-queue]

**[トリガー]** で、**[トリガーの状態]** を設定して継続的インテグレーションを有効にします。  **[保存してキューに登録]** を選択して、ビルドを手動で開始します。

![トリガーを選択する][save-and-queue2]

プッシュまたはチェックイン時にもビルドがトリガーされます。 ビルドの進行状況を確認するには、**[ビルド]** タブに切り替えます。ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース定義を定義します。

### <a name="create-a-release-definition"></a>リリース定義の作成

**[ビルドとリリース]** タブ、**[リリース]**、**[+ 新しい定義]** の順に選択します。  **[テンプレートの選択]** で一覧から **[Azure Service Fabric の配置]** テンプレートを選択し、**[適用]** を選択します。

![リリース テンプレートを選択する][select-release-template]

**[タスク]**->**[環境 1]** の順に選択し、**[+ 新規]** を選択して、新しいクラスター接続を追加します。

![クラスターの接続を追加する][add-cluster-connection]

**[Add new Service Fabric Connection]\(新しい Service Fabric 接続\)** ビューで、**[証明書ベース]** または **[Azure Active Directory]** 認証を選択します。  接続名 "mysftestcluster" とクラスター エンドポイント "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (またはデプロイ先のクラスターのエンドポイント) を指定します。

証明書ベースの認証の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加します。  **[クライアント証明書]** で、クライアント証明書ファイルの Base 64 エンコーディングを追加します。 証明書の Base 64 でエンコードされた表現を取得する方法については、そのフィールドのヘルプ ポップアップをご覧ください。 また、証明書の**パスワード**も追加します。  別のクライアント証明書がない場合は、クラスター証明書またはサーバー証明書を使用できます。

Azure Active Directory の資格情報の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加し、**[ユーザー名]** フィールドと **[パスワード]** フィールドにクラスターへの接続で使用する資格情報を指定します。

**[追加]** をクリックして、クラスター接続を保存します。

次に、ビルド成果物をパイプラインに追加して、リリース定義でビルドの出力を見つけられるようにします。 **[パイプライン]** を選択し、**[成果物]**->**[+ 追加]** の順に選択します。  **[ソース (ビルド定義)]** で、前に作成したビルド定義を選択します。  **[追加]** をクリックして、ビルド成果物を保存します。

![成果物を追加する][add-artifact]

ビルドの完了時にリリースが自動的に作成されるように、継続的配置トリガーを有効にします。 成果物の稲妻のアイコンをクリックし、トリガーを有効にします。次に、**[保存]** をクリックして、リリース定義を保存します。

![トリガーを有効にする][enable-trigger]

**[+ リリース]** -> **[リリースの作成]** -> **[作成]** を選択してリリースを手動で作成します。  デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開いて、[http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/) にアクセスします。  この例では、アプリケーション バージョンは「1.0.0.20170616.3」となっていることに注意してください。

## <a name="commit-and-push-changes-trigger-a-release"></a>変更のコミットとプッシュ、リリースのトリガー

Team Services へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーから [保留中の変更] アイコン (![Pending][pending]) を選択して、ローカル Git リポジトリへ変更をコミットします。

チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

![すべてコミットする][changes]

発行されていない変更のステータス バー アイコンを選択 (![発行されていない変更][unpublished-changes]) するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Team Services と TFS へコードを更新します。

![変更をプッシュする][push]

Team Services へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド定義が正常に完了すると、リリースは自動的に作成され、クラスター上のアプリケーションのアップグレードが開始されます。

ビルドの進行状況を確認するには、Visual Studio の**チーム エクスプローラー**で **[ビルド]** タブに切り替えます。  ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース定義を定義します。

デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開いて、[http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/) にアクセスします。  この例では、アプリケーション バージョンが "1.0.0.20170815.3" である点に注意してください。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>アプリケーションの更新

アプリケーションで、コードを変更します。  前の手順に従って、変更を保存し、コミットします。

アプリケーションのアップグレードが開始されると、Service Fabric Explorer でアップグレードの進行状況を見ることができます。

![Service Fabric Explorer][sfx2]

アプリケーションのアップグレードには数分かかる場合があります。 アップグレードが完了すると、アプリケーションは次のバージョンで実行されます。  この例では "1.0.0.20170815.4" です。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * ビルド定義の作成
> * リリース定義の作成
> * アプリケーションを自動的にデプロイおよびアップグレードする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
