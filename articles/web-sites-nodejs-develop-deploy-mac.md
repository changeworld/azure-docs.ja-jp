<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Web サイト" pageTitle="Mac での Node.js Web サイトの作成 - Azure チュートリアル" metaKeywords="Azure Web サイト作成 Node, Azure Web サイト展開 Node, Web サイト Node.js, Node Web サイト" description="Azure で Node.js Web サイトを構築して展開する方法について説明します。サンプル コードは Java で記述されています。" metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Node.js Web サイトの構築と Azure への展開" authors=""  solutions="" writer="" manager="" editor=""  />





# Node.js Web サイトの構築と Azure への展開

このチュートリアルでは、[Node] アプリケーションを作成し、[Git] を使用して Azure の Web サイトに展開する方法を説明します。このチュートリアルの手順は、Node を実行できる任意のオペレーティング システムで使用できます。

このチュートリアルのビデオを見る場合は、右側のクリップでこのチュートリアルと同じ手順が表示されます。
 
完成したアプリケーションのスクリーンショットは次のようになります。

!["Hello World" メッセージを表示しているブラウザー][helloworld-completed]

##Azure の Web サイトの作成と Git 発行の有効化

Azure の Web サイトを作成し、Web サイトの Git 発行を有効にするには、次のステップに従います。

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p>
</div>

1. [Azure の管理ポータル]にログインします。

2. ポータルの左下にある **[+ 新規]** アイコンをクリックします。

    ![[+ 新規] リンクが強調表示された Azure ポータル][portal-new-website]

3. **[Web サイト]** をクリックし、**[簡易作成]** をクリックします。**[URL]** ボックスに値を入力し、**[リージョン]** ボックスの一覧で Web サイトのデータセンターを選択します。ダイアログの下部にあるチェックマークをクリックします。

    ![[簡易作成] ダイアログ][portal-quick-create]

4. Web サイトの状況が **[実行中]** に変わったら、Web サイトの名前をクリックして**ダッシュボード**にアクセスします。

	![Web サイトのダッシュボードを開く][go-to-dashboard]

5. クイックスタート ページの右下隅で、**[ソース管理からの展開の設定]** を選択します。

	![Git 発行の設定][setup-git-publishing]

6. ソース コードの位置をたずねるメッセージが表示されたら、**[ローカル Git リポジトリ]** を選択し、矢印をクリックします。

	![ソース コードの位置][where-is-code]

7. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。以前に Azure の Web サイトの発行を有効にしたことがある場合、ユーザー名やパスワードの入力は要求されません。代わりに、以前に指定したユーザー名とパスワードを使用して Git リポジトリが作成されます。このユーザー名とパスワードは、作成するすべての Azure の Web サイトへの Git 発行に使用されるのでメモしておきます。

	![ユーザー名とパスワードの入力を求めるダイアログ][portal-git-username-password]

8. Git リポジトリの準備ができると、ローカル リポジトリを設定し、ファイルを Azure にプッシュするために使用する Git コマンドに関する手順が表示されます。

	![Web サイトのリポジトリの作成後に返される Git 展開の手順][git-instructions]

##アプリケーションの作成とローカル テスト

ここでは、[nodejs.org] から入手した 'hello world' の例を含む **server.js** ファイルを作成します。この例は、Azure の Web サイトで実行するときにリッスンするポートとして process.env.PORT を追加することで、元の例から変更されています。

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
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

4. Web ブラウザーを開き、http://localhost:1337/ に移動します。次のスクリーンショットのように、Web ページが開き "Hello World" と表示されます。

    !["Hello World" メッセージを表示しているブラウザー][helloworld-localhost]

##アプリケーションの発行

1. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを入力してローカル Git リポジトリを初期化します。

		git init

	<div class="dev-callout"><strong>Git コマンドが使用できない場合</strong>
	<p><a href="http://git-scm.com/" target="_blank">Git</a> は、Azure の Web サイトを展開するために使用できる分散型バージョン コントロール システムです。お使いのプラットフォームでのインストールの手順については、<a href="http://git-scm.com/download" target="_blank">Git のダウンロード ページ</a>を参照してください。</p>
	</div>

2. 次のコマンドを使用して、リポジトリにファイルを追加します。

		git add .
		git commit -m "initial commit"

3. 次のコマンドを使用して、先ほど作成した Azure の Web サイトに更新をプッシュするために Git リモートを追加します。

		git remote add azure [URL for remote repository]

    ![Web サイトのリポジトリの作成後に返される Git 展開の手順][git-instructions]
 
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
    
	管理ポータル内で Azure の Web サイトの [展開] タブに移動すると、展開履歴に最初の展開が表示されます。

	![ポータルでの Git 展開の状態][git-deployments-first]

5. 管理ポータル内で、Azure の Web サイトのページの **[参照]** ボタンを使用してサイトに移動します。

##アプリケーションへの変更の発行

1. テキスト エディターで **server.js** ファイルを開き、'Hello World\n' を 'Hello Azure\n' に変更します。ファイルを保存します。
2. コマンド ラインから、**helloworld** ディレクトリに移動し、次のコマンドを実行します。

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。管理ポータル内で Azure の Web サイトの [展開] タブに移動すると、更新された展開履歴が表示されます。
	
	![ポータルで更新された Git 展開の状態][git-deployments-second]

3. **[参照]** ボタンを使用してサイトに移動し、更新が適用されたことを確認します。

	!["Hello Azure" と表示している Web ページ][helloworld-completed]

4. 管理ポータル内で、Azure の Web サイトの [展開] タブで前の展開を選択し、**[再展開]** ボタンを使用して、前の展開に戻すことができます。

##次のステップ

この記事の手順では、Azure ポータルを使用して Web サイトを作成しましたが、[Mac および Linux 用 Azure コマンド ライン ツール]を使用して同じ操作を行うこともできます。

Node.js は、アプリケーションで使用できるモジュールのリッチなエコシステムを実現します。Azure の Web サイトでモジュールを使用する方法については、「[Azure アプリケーションでの Node.js モジュールの使用](http://www.windowsazure.com/ja-jp/develop/nodejs/common-tasks/working-with-node-modules/)」を参照してください。

Azure に付属している Node.js のバージョンの詳細については、「[Azure アプリケーションでの Node.js のバージョンの指定](http://www.windowsazure.com/ja-jp/develop/nodejs/common-tasks/specifying-a-node-version/)」を参照してください。

Azure の展開後にアプリケーションに問題が発生した場合、問題の診断については、「[How to debug a Node.js application in Azure Web Sites (Azure の Web サイトでの Node.js アプリケーションのデバッグ方法)](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/Debug-Website/)」を参照してください。


##その他のリソース

* [Azure PowerShell]
* [Mac および Linux 用 Azure コマンド ライン ツール]

[Azure PowerShell]: /ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/



[Azure の管理ポータル]: http://manage.windowsazure.com
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/

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

