<properties
	pageTitle="Azure App Service での Node.js Web アプリの作成"
	description="Azure で Node.js Web アプリを構築してデプロイする方法について説明します。"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Azure App Service での Node.js Web アプリの構築とデプロイ

このチュートリアルでは、[Node][nodejs.org] アプリケーションを作成し、[Git] を使用して [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法を説明します。このチュートリアルの手順は、Node を実行できる任意のオペレーティング システムで使用できます。

完成したアプリケーションのスクリーンショットは次のようになります。

!["Hello World" メッセージを表示しているブラウザー][helloworld-completed]

##Web アプリの作成と Git 発行の有効化

Web アプリを作成して、Git 発行を有効にするには、次の手順に従います。

> [AZURE.NOTE]
> このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[MSDN サブスクライバーの特典を有効にする](/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)か、[無料試用版にサインアップ](/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F)してください。

1. [Azure ポータル](https://portal.azure.com)にログインします。

2. ポータルの左上にある **[+ 新規]** アイコンをクリックします。

3. **[Web + モバイル]** をクリックし、**[Web アプリ]** をクリックします。

    ![][portal-quick-create]

4. **[URL]** の値を入力します。

5. App Service プランを選択するか、新しいプランを作成します。新しいプランを作成する場合は、価格レベル、および場所などのオプションを選択します。

    ![][portal-quick-create2]

6. **[作成]** をクリックします。

7. 状態が **[実行中]** に変わると、Web アプリのブレードが自動的に表示されます。**[参照]** をクリックして、ブレードに到達することもできます。

	![][go-to-dashboard]

8. **[デプロイ]** をクリックします。ブレードをこのパーツまでスクロールする必要がある場合があります。

	![][deployment-part]

9. **[ソースの選択]** をクリックし、**[ローカル Git リポジトリ]** をクリックします。**[OK]** をクリックします。

	![][setup-git-publishing]


10. **デプロイ資格情報**のパーツをクリックします (下に赤のアウトラインで示されています)。ユーザー名とパスワードを作成します。**[保存]** をクリックします。既に Web アプリの発行を有効にしている場合、このステップは必要ありません。

	![][deployment-credentials]


11. 発行するには、Git リモート リポジトリにプッシュします。リポジトリの URL を見つけ、**[すべての設定]** をクリックし、**[プロパティ]** をクリックします。URL は "GIT URL" の下に表示されます。

	![][git-url]

##アプリケーションの作成とローカル テスト

ここでは、**nodejs.org** から入手した 'hello world' の例を含む [server.js] ファイルを作成します。この例は、Azure Web アプリで実行するときにリッスンするポートとして process.env.PORT を追加することで、元の例から変更されています。

1. テキスト エディターを使用して、**helloworld** ディレクトリに **server.js** という名前の新しいファイルを作成します。**helloworld** ディレクトリがない場合は作成します。

2. **server.js** ファイルの内容として次の内容を追加し、ファイルを保存します。

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. コマンド ラインを開き、次のコマンドを使用してローカルで Web アプリを開始します。

        node server.js

4. Web ブラウザーを開いて、http://localhost:1337 にアクセスします。次のスクリーンショットのように、Web ページが開き "Hello World" と表示されます。

    !["Hello World" メッセージを表示しているブラウザー][helloworld-localhost]

##アプリケーションの発行

1. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを入力してローカル Git リポジトリを初期化します。

		git init

	> [AZURE.NOTE]**Git コマンドが使用できない場合** 
	> [Git](http://git-scm.com/%20target="_blank) は、Azure の Web サイトをデプロイするために使用できる分散型バージョン コントロール システムです。お使いのプラットフォームでのインストールの手順については、[Git のダウンロード ページ](http://git-scm.com/download%20target="_blank")を参照してください。

2. 次のコマンドを使用して、リポジトリにファイルを追加します。

		git add .
		git commit -m "initial commit"

3. 次のコマンドを使用して、先ほど作成した Azure Web アプリに更新をプッシュするために Git リモートを追加します。

		git remote add azure [URL for remote repository]


4. 次のコマンドを使用して、変更内容を Azure にプッシュします。

		git push azure master

	先ほど作成したパスワードを入力するように求められます。出力は次のようになります。

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. アプリを表示するには、管理ポータルで **[Web アプリ]** パーツの **[参照]** ボタンをクリックします。

##アプリケーションへの変更の発行

1. テキスト エディターで **server.js** ファイルを開き、'Hello World\n' を 'Hello Azure\n' に変更します。ファイルを保存します。
2. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを実行します。

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

3. **[参照]** をクリックしてアプリに移動し、更新が適用されたことを確認します。

	!["Hello Azure" と表示している Web ページ][helloworld-completed]

4. **[デプロイ]** で過去のデプロイを選択し、それに戻すことができます。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##次のステップ

この記事の手順では、Azure ポータルを使用して Web アプリを作成しましたが、[Azure コマンドインターフェイス](../xplat-cli.md)を使用して同じ操作を行うこともできます。

Node.js は、アプリケーションで使用できるモジュールのリッチなエコシステムを実現します。Web Apps でモジュールを使用する方法については、「[Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)」を参照してください。

Azure に付属している Node.js のバージョンの詳細と、アプリケーションで使用するバージョンの指定方法については、「[Azure アプリケーションでの Node.js のバージョンの指定](../nodejs-specify-node-version-azure-apps.md)」を参照してください。

Azure へのデプロイ後にアプリケーションで問題が発生した場合、問題の診断については、「[Azure の Web サイトでの Node.js アプリケーションのデバッグ方法](web-sites-nodejs-debug.md)」を参照してください。


##その他のリソース

* [Azure PowerShell](../install-configure-powershell.md)
* [Azure コマンド ライン インターフェイス](../xplat-cli.md)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。


[nodejs.org]: http://nodejs.org
[server.js]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=July15_HO4-->