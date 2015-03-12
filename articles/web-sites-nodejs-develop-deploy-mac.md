<properties 
	pageTitle="Mac での Node.js Web サイトの作成 - Azure チュートリアル" 
	description="Azure で Node.js Web サイトを構築してデプロイする方法について説明します。サンプル コードは Java で記述されています。" 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>






# Node.js Web サイトの構築と Azure へのデプロイ

このチュートリアルでは、[Node] [nodejs.org] アプリケーションを作成し、[Git] を使用して Azure Web サイトにデプロイする方法を説明します。このチュートリアルの手順は、Node を実行できる任意のオペレーティング システムで使用できます。

このチュートリアルを動画で視聴したい場合は、次のクリップで同様の手順を確認できます。
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
完成したアプリケーションのスクリーンショットは次のようになります。

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Azure Web サイトの作成と Git 発行の有効化

Azure Web サイトを作成し、Web サイトの Git 発行を有効にするには、次の手順に従います。

> [AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料評価版サイト</a> をご覧ください。
> 
> アカウントにサインアップする前に Azure Web サイトを実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Web サイトで、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

1. [Azure 管理ポータル] にログインします。

2. ポータルの左下にある **[+ 新規]** アイコンをクリックします。

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. **[Web サイト]**をクリックし、 **[簡易作成]** をクリックします。**URL** ボックスに値を入力し、**[リージョン]** ドロップダウンで Web サイトのデータセンターを選択します。ダイアログの下部にあるチェックマークをクリックします。

    ![The Quick Create dialog][portal-quick-create]

4. Web サイトの状況が **[実行中]** に変わったら、Web サイトの名前をクリックして**[ダッシュボード]** にアクセスします。

	![Open web site dashboard][go-to-dashboard]

6. クイックスタート ページの右下隅で、**[ソース管理からの展開の設定]** を選択します。

	![Set up Git publishing][setup-git-publishing]

6. ソース コードの位置をたずねるメッセージが表示されたら、**[ローカル Git リポジトリ]** を選択し、矢印をクリックします。

	![where is your source code][where-is-code]

7. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。以前に Azure の Web サイトの発行を有効にしたことがある場合、ユーザー名やパスワードの入力は要求されません。代わりに、以前に指定したユーザー名とパスワードを使用して Git リポジトリが作成されます。このユーザー名とパスワードは、作成するすべての Azure Web サイトへの Git 発行に使用されるのでメモしておきます。

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Git リポジトリの準備ができると、ローカル リポジトリを設定し、ファイルを Azure にプッシュするために使用する Git コマンドに関する手順が表示されます。

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##アプリケーションの作成とローカル テスト

ここでは、[nodejs.org] から入手した  'hello world' の例を含む **server.js** ファイルを作成します。この例は、Azure Web サイトで実行するときにリッスンするポートとして process.env.PORT を追加することで、元の例から変更されています。

1. テキスト エディターを使用して、**helloworld** ディレクトリに **server.js** という名前の新しいファイルを作成します。**helloworld** ディレクトリがない場合は作成します。
2. **server.js** ファイルの内容として次の内容を追加し、ファイルを保存します。

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. コマンド ラインを開き、次のコマンドを使用してローカルで Web ページを開始します。

        node server.js

4. Web ブラウザーを開き、http://localhost:1337 に移動します。次のスクリーンショットのように、Web ページが開き "Hello World" と表示されます。

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##アプリケーションの発行

1. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを入力してローカル Git リポジトリを初期化します。 

		git init

	> [AZURE.NOTE] **Git コマンドが使用できない場合**
	[Git](http://git-scm.com/%20target="_blank) は、Azure Web サイトのデプロイに使用できる分散型バージョン コントロール システムです。お使いのプラットフォームでのインストールの手順については、[Git のダウンロード ページ](http://git-scm.com/download%20target="_blank")をご覧ください。

2. 次のコマンドを使用して、リポジトリにファイルを追加します。

		git add .
		git commit -m "initial commit"

3. 次のコマンドを使用して、先ほど作成した Azure Web サイトに更新をプッシュするために Git リモートを追加します。

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. 次のコマンドを使用して、変更内容を Azure にプッシュします。

		git push azure master

	先ほど作成したパスワードを入力するように求められ、次のような出力が表示されます。

		Password for 'testsite.scm.azurewebsites.net':
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
    
	管理ポータル内で Azure Web サイトの [展開] タブに移動すると、展開履歴に最初の展開が表示されます。

	![Git deployment status on the portal][git-deployments-first] 

5. 管理ポータル内で、Azure Web サイトのページの **[参照]** ボタンを使用してサイトに移動します。

##アプリケーションへの変更の発行

1. テキスト エディターで **server.js** ファイルを開き、 'Hello World\n' を  'Hello Azure\n' に変更します。ファイルを保存します。
2. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを実行します。

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。管理ポータル内で Azure Web サイトの [展開] タブに移動すると、更新された展開履歴が表示されます。
	
	![Git deployment status updated on the portal][git-deployments-second]

3. **[参照]** ボタンを使用してサイトに移動し、更新が適用されたことを確認します。

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. 管理ポータル内で、Azure Web サイトの [展開] タブで前の展開を選択し、**[再展開]** ボタンを使用して、前の展開に戻すことができます。

##次のステップ

この記事の手順では、Azure ポータルを使用して Web サイトを作成しましたが、[Mac および Linux 用 Azure コマンド ライン ツール] を使用して同じ操作を行うこともできます。

Node.js は、アプリケーションで使用できるモジュールのリッチなエコシステムを実現します。Azure Web サイトでモジュールを使用する方法については、「[Using Node.js Modules with Azure Applications (Azure アプリケーションでの Node.js モジュールの使用)](/ja-jp/documentation/articles/nodejs-use-node-modules-azure-apps/).」をご覧ください

Azure に付属している Node.js のバージョンの詳細と、アプリケーションで使用するバージョンの指定方法については、「[Specifying a Node.js version in an Azure application (Azure アプリケーションでの Node.js のバージョンの指定)](/ja-jp/documentation/articles/nodejs-specify-node-version-azure-apps/).」をご覧ください

Azure への展開後にアプリケーションで問題が発生した場合は、「[How to debug a Node.js application in Azure Web Sites (Azure の Web サイトでの Node.js アプリケーションのデバッグ方法)](/ja-jp/documentation/articles/web-sites-nodejs-debug/) for information on diagnosing the problem.」をご覧ください


##その他のリソース

* [Azure PowerShell]
* [Mac および Linux 用 Azure コマンド ライン ツール]

[Azure PowerShell]: /ja-jp/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Azure 管理ポータル]: http://manage.windowsazure.com
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png


<!--HONumber=42-->
