---
title: "継続的インテグレーションを使用して Azure Service Fabric アプリケーションをデプロイする (Team Services) | Microsoft Docs"
description: "Visual Studio Team Services を使用して、継続的インテグレーションと Service Fabric アプリケーションのデプロイを設定する方法について説明します。  Azure の Service Fabric クラスターにアプリケーションをデプロイします。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: dda5ea77b35fa8491128135ea7709016781f2aea
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>CI/CD を使用して Service Fabric クラスターへアプリケーションをデプロイする
このチュートリアルはシリーズの第 3 部です。Visual Studio Team Services を使用して、Azure Service Fabric アプリケーションの継続的インテグレーションとデプロイを設定する方法について説明します。  既存の Service Fabric アプリケーションが必要で、[.NET アプリケーション ビルド](service-fabric-tutorial-create-dotnet-app.md)で作成されたアプリケーションを例として使用します。

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
> * Visual Studio Team Services を使用して CI/CD を構成する

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。
- [このチュートリアルに従って](service-fabric-tutorial-create-dotnet-app.md)、たとえば、Service Fabric アプリケーションを作成します。 
- [このチュートリアルに従って](service-fabric-tutorial-create-cluster-azure-ps.md)、たとえば、Azure 上に Windows Service Fabric クラスターを作成します。
- [Team Services アカウント](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) を作成します。

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
Visual Studio のウィザード内のビルドを使用して、VSTS を利用した継続的配信を構成します。

1. **ソリューション エクスプローラー**で **[MyApplication]** アプリケーション プロジェクトを右クリックし、**[** -> **VSTS を利用した継続的配信の追加]** を選択します。 必要な構成を入力するためのダイアログ ボックスが表示されます。

    ![VSTS を利用した継続的配信][continuous-delivery-with-VSTS]

    **[アカウント]**、**[プロジェクト]**、および **[Git リポジトリ]** 値が自動的に入力されます。

2. エージェント キューとして **[ホステッド VS2017]** を選択します。

3. **[クラスター接続]** ドロップダウンで **[接続の作成]** を選択すると、VSTS でサービス エンドポイントを構成する Web サイトが開きます。 

4. ブラウザー ウィンドウにフォーカスを設定して、**[新しいサービス エンドポイント]** -> **[Service Fabric]** を選択します。

    ![新しいサービス エンドポイント][new-service-endpoint]

    新しい Service Fabric 接続を追加するダイアログ ボックスが表示されます。
    
5. **[証明書ベース]** を選択し、ダイアログを完了します。

    ![[新しいサービス エンドポイント] ダイアログ][new-service-endpoint-dialog]

    1. **[接続名]** を入力します。
    2. クラスターの管理エンドポイント (たとえば、"tcp://mycluster.eastus.cloudapp.azure.com:19000") を、**[クラスター エンドポイント]** フィールドに入力します。 プロトコルとして「tcp://」を指定します。  既定の管理エンドポイント ポートは、「19000」です。
    3. [**サーバー証明書サムプリント**] を入力します。  サムプリントは、クラスター (https://mycluster.eastus.cloudapp.azure.com:19080) の Service Fabric Explorer を開くと取得できます。
        - ツリー ビューの **[クラスター]** ノードと、右側のウィンドウにある **[マニフェスト]** タブを選択します。
        - XML ファイル内で**<ServerCertificate>**要素を検索し、**X509FindValue** 属性の内容をコピーします。
    4. **[クライアント証明書]** フィールドに、ビルド エージェントにインストールされている証明書を取得するために必要な、Base64 でエンコードされた文字列としての証明書を入力します。
        - 使用可能な PFX ファイルとして証明書があることを確認してください。
        - クリップボードに Base64 でエンコードされた文字列として PFX ファイルを出力する場合は、次の PowerShell コマンドを実行します。`[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - クリップボードから、値をダイアログ ボックスのフィールドに貼り付け (ctrl キーを押しながら v) ます。
    5. **[パスワード]** フィールドに **[証明書のパスワード]** を入力し、**[OK]** をクリックします。

6. Visual Studio の **[VSTS を利用した継続的な配信]** ダイアログの **[クラスター接続]** フィールドで**<Refresh>**選択します。 先ほど作成したクラスター エンドポイントが、ドロップダウン リストに表示されるはずです。

7. 既定のビルドおよびリリース定義名を選択するか、またはダイアログ ボックスで提案された名前を変更します。 完了したら **[OK]** をクリックします。

しばらくすると、ブラウザーでビルドとリリース定義を開くかどうかたずねるダイアログ ボックスが Visual Studio に表示されます。 構成状態を調べるためにブラウザーで開くことを選択できますが、このチュートリアルを完了するためには必要ありません。

- Team Services のビルド定義では、順次実行される一連のビルド ステップで構成されたワークフローを記述します。 Service Fabric クラスターをデプロイするため、Service Fabric アプリケーション パッケージおよび他のアーティファクトを生成するビルド定義を作成します。 Team Services のビルド定義の詳細については、[こちら](https://www.visualstudio.com/docs/build/define/create)をご覧ください。
- Team Services のリリース定義では、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します ビルド定義とリリース定義を併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Team Services のリリース定義の詳細については、 [こちら](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)をご覧ください。

## <a name="commit-and-push-changes-trigger-a-release"></a>変更のコミットとプッシュ、リリースのトリガー
Team Services へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。    

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーから [保留中の変更] アイコン (![Pending][pending]) を選択して、ローカル Git リポジトリへ変更をコミットします。

チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

![すべてコミットする][changes]

発行されていない変更のステータス バー アイコンを選択 (![発行されていない変更][unpublished-changes]) するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Team Services と TFS へコードを更新します。

![変更をプッシュする][push]

Team Services へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド定義が正常に完了すると、リリースは自動的に作成され、クラスター上のアプリケーションのアップグレードが開始されます。

ビルドの進行状況を確認するには、Visual Studio の**チーム エクスプローラー**で **[ビルド]** タブに切り替えます。  ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース定義を定義します。

デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開き、[http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/) に移動します。  この例では、アプリケーション バージョンは「1.0.0.20170616.3」となっていることに注意してください。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>アプリケーションの更新
アプリケーションで、コードを変更します。  前の手順に従って、変更を保存し、コミットします。

アプリケーションのアップグレードが開始されると、Service Fabric Explorer でアップグレードの進行状況を見ることができます。

![Service Fabric Explorer][sfx2]

アプリケーションのアップグレードには数分かかる場合があります。 アップグレードが完了すると、アプリケーションは次のバージョンで実行されます。  この例では「1.0.0.20170616.4」です。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * ビルド定義の作成
> * リリース定義の作成
> * アプリケーションを自動的にデプロイおよびアップグレードする

このチュートリアルでは、アプリケーションをデプロイして、継続的インテグレーションを構成しました。次の操作をお試しください。
- [アプリケーションのアップグレード](service-fabric-application-upgrade.md)
- [アプリケーションのテスト](service-fabric-testability-overview.md) 
- [監視と診断](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
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
