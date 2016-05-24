<properties 
	pageTitle="5 分で初めての Web アプリを Azure にデプロイする" 
	description="わずかな手順でサンプル アプリをデプロイして、App Service で Web アプリを簡単に実行できることを説明します。実際の開発を 5 分で開始し、すぐに成果を確認できます。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="05/12/2016" 
	ms.author="cephalin"
/>
	
# 5 分で初めての Web アプリを Azure にデプロイする

[AZURE.INCLUDE [タブ](../../includes/app-service-web-get-started-nav-tabs.md)]

このチュートリアルでは、初めての Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) にデプロイします。App Service を使用すると、Web アプリ、[モバイル アプリ バックエンド](/documentation/learning-paths/appservice-mobileapps/)、[API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)を作成できます。

必要な操作は次のようにわずかです。

- サンプルの Web アプリケーションをデプロイする (ASP.NET、PHP、Node.js、Java、Python から選択する)。
- すぐにアプリがライブ実行されるのを確認する。
- [Git コミットをプッシュする](https://git-scm.com/docs/git-push)ときと同じ方法で Web アプリを更新する。

また、[Azure ポータル](https://portal.azure.com)を確認し、使用可能な機能を調べます。

## 前提条件

- [Git をインストールします](http://www.git-scm.com/downloads)。 
- [Azure CLI をインストールします](../xplat-cli-install.md)。 
- Microsoft Azure アカウントを取得します。アカウントを持っていない場合は、[無料試用版にサインアップする](/pricing/free-trial/?WT.mc_id=A261C142F)か [Visual Studio サブスクライバー特典を有効](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

>[AZURE.NOTE] Web アプリの動作を確認してください。今すぐ [App Service を試用](http://go.microsoft.com/fwlink/?LinkId=523751)して、有効期間が短いスターター アプリを作成してみてください。このサービスの利用にあたり、クレジット カードや契約は必要ありません。

## Web アプリをデプロイする

Azure App Service に Web アプリをデプロイしましょう。

1. 新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルを開きます。`git --version` と `azure --version` を実行し、Git と Azure CLI がコンピューターにインストールされていることを確認します。 

    ![Test installation of CLI tools for your first web app in Azure](./media/app-service-web-get-started/1-test-tools.png)

    ツールをインストールしていない場合は、「[前提条件](#Prerequisites)」のダウンロード リンクを参照してください。

1. `CD` で作業ディレクトリに移動し、次のようにサンプル アプリを複製します。

        git clone <github_sample_url>

    ![Clone the app sample code for your first web app in Azure](./media/app-service-web-get-started/2-clone-sample.png)

    *&lt;github\_sample\_url>* には、好みのフレームワークに応じて次の URL のいずれかを使用します。

    - HTML + CSS + JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. `CD` でサンプル アプリのリポジトリに移動します。たとえば、次のように入力します。

        cd app-service-web-html-get-started

3. 次のようにして、Azure にログインします。

        azure login
    
    ヘルプ メッセージに従って、ログイン プロセスを続行します。
    
    ![Log in to Azure to create your first web app](./media/app-service-web-get-started/3-azure-login.png)

4. 次のコマンドを使用し、一意のアプリ名で Azure に App Service アプリ リソースを作成します。メッセージが表示されたら、必要なリージョンの数を指定します。

        azure site create --git <app_name>
    
    ![Create the Azure resource for your first web app in Azure](./media/app-service-web-get-started/4-create-site.png)
    
    >[AZURE.NOTE] Azure サブスクリプションのデプロイ資格情報がまだ設定されていない場合は、それらを作成するように求められます。これらの資格情報 (Azure アカウント資格情報ではありません) は App Service で Git のデプロイと FTP のログインのみに使用されます。
    
    これでアプリが Azure で作成されました。また、現在のディレクトリが Git として初期化され、この新しい App Service アプリに Git リモートとして接続されています。アプリの URL (http://&lt;app_name>.azurewebsites.net) を参照すると既定の美しい HTML ページが表示されますが、ここでは用意したコードを実際に使用しましょう。

4. Git でコードをプッシュする場合と同じように、サンプル コードを新しい App Service アプリにデプロイします。

        git push azure master 

    ![Push code to your first web app in Azure](./media/app-service-web-get-started/5-push-code.png)
    
    いずれかの言語フレームワークを使用した場合は、上に示したものとは別の出力が表示されます。`git push` を実行すると、Azure にコードが配置されるだけでなく、デプロイ エンジンのデプロイ タスクがトリガーされるためです。プロジェクト (リポジトリ) のルートに package.json (Node.js) または requirements.txt (Python) がある場合、または ASP.NET プロジェクトに packages.config がある場合は、デプロイメント スクリプトによって必要なパッケージが復元されます。また、[Composer 拡張機能を有効にして](web-sites-php-mysql-deploy-use-git.md#composer)、PHP アプリで composer.json ファイルを自動的に処理することもできます。

これで、Azure App Service にアプリがデプロイされました。

## アプリがライブ実行されるのを確認する

Azure で実稼働しているアプリを確認するには、リポジトリ内の任意のディレクトリから次のコマンドを実行します。

    azure site browse

## アプリを更新する

Git を使用してプロジェクト (リポジトリ) のルートからプッシュして、いつでもライブ サイトを更新することができるようになりました。その方法は、初めて Azure にアプリをデプロイしたときの方法と同じです。たとえば、ローカルでテストした新しい変更をプッシュする場合は、プロジェクト (リポジトリ) のルートから次のコマンドを実行するだけで済みます。
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Azure ポータルでのアプリの確認

Azure ポータルにアクセスして、作成したアプリを確認しましょう。

1. Azure サブスクリプションのある Microsoft アカウントを使用して、[Azure ポータル](https://portal.azure.com)にログインします。

2. 左側のバーで **[App Services]** をクリックします。

3. 作成したアプリをクリックして、ポータルでそのページを開きます ([ブレード](../azure-portal-overview.md)と呼ばれています)。既定で **[設定]** ブレードも開くので便利です。

    ![Portal view of your first web app in Azure](./media/app-service-web-get-started/portal-view.png)

App Service アプリのポータル ブレードには、アプリの構成、監視、セキュリティ保護、トラブルシューティングを実行するための豊富な設定とツールが表示されます。単純なタスクをいくつか実行して、このインターフェイスに慣れるようにしてください (タスクの番号は、スクリーン ショットの番号に対応しています)。

1. アプリを停止する
2. アプリを再起動する
3. **[リソース グループ]** リンクをクリックし、リソース グループにデプロイされたすべてのリソースを表示する
4. **[設定]**、**[プロパティ]** の順にクリックし、アプリに関するその他の情報を表示する
5. **[ツール]** をクリックし、監視とトラブルシューティングに役立つツールにアクセスする  

## 次のステップ

- Azure アプリを次のレベルに進めます。アプリを認証で保護したり、必要に応じてスケールを変更したり、パフォーマンスのアラートを設定したりできます。いずれも、数回のクリックで実現できます。[初めての Web アプリへの機能の追加](app-service-web-get-started-2.md)に関するページを参照してください。
- Git と Azure CLI の他にも、Web アプリを Azure にデプロイする方法があります (「[Azure App Service へのアプリのデプロイ](../app-service-web/web-sites-deploy.md)」を参照してください)。記事の上部で言語フレームワークを選択すると、そのフレームワーク用の開発とデプロイの手順を参照できます。

<!---HONumber=AcomDC_0518_2016-->