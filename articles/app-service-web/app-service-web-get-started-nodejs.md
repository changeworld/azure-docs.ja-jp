---
title: "初めての Node.js Web アプリを Azure に 5 分でデプロイする (CLI 2.0 プレビュー) | Microsoft Docs"
description: "サンプル Node.js アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。 実際の開発を速やかに開始し、すぐに成果を確認できます。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 06a8dfbac31024cb44fd38bcf9c4a4ea79dbc968


---
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes-cli-20-preview"></a>初めての Node.js Web アプリを Azure に 5 分でデプロイする (CLI 2.0 プレビュー)

> [!div class="op_single_selector"]
> * [初めての HTML サイト](app-service-web-get-started-html.md)
> * [初めての .NET アプリ](app-service-web-get-started-dotnet.md)
> * [初めての PHP アプリ](app-service-web-get-started-php.md)
> * [初めての Node.js アプリ](app-service-web-get-started-nodejs.md)
> * [初めての Python アプリ](app-service-web-get-started-python.md)
> * [初めての Java アプリ](app-service-web-get-started-java.md)
> 
> 

このチュートリアルでは、初めての Node.js Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md)にデプロイします。
App Service を使用すると、Web アプリ、[モバイル アプリ バックエンド](/documentation/learning-paths/appservice-mobileapps/)、および [API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)を作成できます。

このチュートリアルの内容は次のとおりです。 

* Azure App Service で Web アプリを作成する。
* Node.js のサンプル コードをデプロイする。
* 運用環境でライブ実行されているコードを確認する。
* [Git コミットをプッシュする](https://git-scm.com/docs/git-push)ときと同じ方法で Web アプリを更新する。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](app-service-web-get-started-nodejs-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0 (プレビュー)](app-service-web-get-started-nodejs.md) - Resource Manager デプロイメント モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI 2.0 プレビュー](/cli/azure/install-az-cli2)。
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="deploy-a-nodejs-web-app"></a>Node.js Web アプリをデプロイする
1. 新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルを開きます。 `git --version` と `azure --version` を実行し、Git と Azure CLI がコンピューターにインストールされていることを確認します。
   
    ![Test installation of CLI tools for your first web app in Azure](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    ツールをインストールしていない場合は、「 [前提条件](#Prerequisites) 」のダウンロード リンクを参照してください。
2. 次のようにして、Azure にログインします。
   
        az login
   
    ヘルプ メッセージに従って、ログイン プロセスを続行します。
   
    ![Log in to Azure to create your first web app](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. App Service のデプロイ ユーザーを設定します。 後で、これらの資格情報を使用してコードをデプロイします。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 この最初の App Service のチュートリアルでは、実際にその内容を把握している必要はありません。

        az group create --location "<location>" --name my-first-app-group

    `<location>` に使用できる値を確認するには、CLI コマンド `az appservice list-locations` を使用してください。

3. 新しい "Free" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 この最初の App Service のチュートリアルでは、このプランの Web アプリに対しては課金されないことを把握しておくだけでかまいません。

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. `<app_name>` に一意の名前を指定して新しい Web アプリを作成します。

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. 次に、デプロイするサンプル Node.js コードを取得します。 作業ディレクトリに移動 (`CD`) し、次のようにサンプル アプリを複製します。
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

5. サンプル アプリのリポジトリに移動します。
   
        cd app-service-web-nodejs-get-started
5. 次のコマンドを使用して、App Service Web アプリのローカル Git デプロイを構成します。

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    次のような JSON 出力が表示されます。これは、リモート Git リポジトリが構成されていることを意味します。

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. この URL を、ローカル リポジトリの Git リモートとして JSON に追加します (Git リモートは、わかりやすくするために `azure` という名前にしています)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Git でコードをプッシュする場合と同様に、サンプル コードを Azure アプリにデプロイします。 メッセージが表示されたら、前に構成したパスワードを入力します。
   
        git push azure master
   
    ![Push code to your first web app in Azure](./media/app-service-web-get-started-languages/node-git-push.png)
   
    `git push` を実行すると、Azure にコードが配置されるだけでなく、デプロイ エンジンのデプロイ タスクがトリガーされます。 
    プロジェクト (リポジトリ) ルートに package.json がある場合は、デプロイ スクリプトが必要なパッケージを復元します。 

これで、Azure App Service にアプリがデプロイされました。

## <a name="see-your-app-running-live"></a>アプリがライブ実行されるのを確認する
Azure で実稼働しているアプリを確認するには、リポジトリ内の任意のディレクトリから次のコマンドを実行します。

    azure site browse

## <a name="make-updates-to-your-app"></a>アプリを更新する
Git を使用してプロジェクト (リポジトリ) のルートからプッシュして、いつでもライブ サイトを更新することができるようになりました。 これは、初めてコードをデプロイしたときと同様に行います。 たとえば、ローカルでテストした新しい変更をプッシュする場合は、プロジェクト (リポジトリ) のルートから次のコマンドを実行するだけで済みます。

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>次のステップ
[Node.js Express Web アプリを作成および構成して、Azure にデプロイします](app-service-web-nodejs-get-started.md)。 このチュートリアルでは、Azure で任意の Node.js を実行するのに必要となる、以下のような基本的なスキルを学習します。

* PowerShell/Bash から Azure でアプリを作成および構成する。
* Node.js のバージョンを設定する。
* ルート アプリケーション ディレクトリに存在しないスタート ファイルを使用する。
* NPM で自動化する。
* エラー ログと出力ログを取得する。

または、最初の Web アプリを活用します。 次に例を示します。

* [Azure にコードをデプロイする他の方法](web-sites-deploy.md)を試してみます。 たとえば、GitHub リポジトリのいずれかからデプロイする場合、**[デプロイ オプション]** の **[ローカル Git リポジトリ]** ではなく、**[GitHub]** を選択します。
* Azure アプリを次のレベルに進めます。 ユーザーを認証します。 必要に応じてスケールを変更したり、 パフォーマンスのアラートを設定したりできます。 いずれも、数回のクリックで実現できます。 「[初めての Web アプリに機能を追加する](app-service-web-get-started-2.md)」を参照してください。




<!--HONumber=Jan17_HO3-->


