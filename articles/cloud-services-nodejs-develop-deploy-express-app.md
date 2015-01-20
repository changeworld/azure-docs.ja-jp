<properties urlDisplayName="Web App with Express" pageTitle="Express を使用した Web アプリケーション (Node.js) - Azure チュートリアル" metaKeywords="Azure Node.js Hello World チュートリアル, Azure Node.js Hello World, Azure Node.js 概要チュートリアル, Azure Node.js チュートリアル, Azure Node.js Express チュートリアル" description="クラウド サービスのチュートリアルを基に、Express モジュールの使用方法を示すチュートリアル。" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Azure Cloud Services での Express を使用した Node.js Web アプリケーションの構築

node.js には、コア ランタイムの最小限の機能セットが含まれます。多くの場合、開発者は Node.js アプリケーションを開発するときに、サード パーティ モジュールを使用して追加機能を指定します。このチュートリアルでは、Express モジュールを使用して新しいアプリケーションを作成します。Express モジュールは、Node.js Web アプリケーションを作成するための MVC フレームワークを提供します。

完成したアプリケーションのスクリーンショットは次のようになります。

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##クラウド サービス プロジェクトの作成

"expressapp" という名前の新しいクラウド サービス プロジェクトを作成するには、次の手順を実行します。

1. **[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. **c:\node** ディレクトリに移動し、次のコマンドを入力して **expressapp** という名前の新しいソリューションと **WebRole1** という名前の Web ロールを作成します。

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [WACOM.NOTE] 既定では、**Add-AzureNodeWebRole** は古いバージョンの Node.js を使用します。上記の **Set-azureserviceprojectrole** ステートメントは v0.10.21 のノードを使用するように Azure に指示します。 

##Express のインストール

1. 次のコマンドを発行して Express ジェネレーターをインストールします。

		PS C:\node\expressapp> npm install express-generator -g

	この npm コマンドにより次のような結果が出力されます。 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. **WebRole1** ディレクトリに移動し、express コマンドを使用して新しいアプリケーションを生成します。

        PS C:\node\expressapp\WebRole1> express

	前のアプリケーションを上書きするかどうかを確認するメッセージが表示されます。**「y」**または**「yes」**と入力して続行します。Express によって、app.js ファイルと、アプリケーションを構築するためのフォルダー構造が生成されます。

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  package.json ファイルに定義された依存関係をインストールするには、次のコマンドを入力します。

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  次のコマンドを使用して、**bin/www** ファイルを **server.js** にコピーします。これは、クラウド サービスがこのアプリケーションのエントリ ポイントを見つけられるようにするためです。

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	このコマンドが完了すると、WebRole1 ディレクトリ内に **server.js** ファイルが作成されています。

7.  **server.js** を変更して、次の行から '.' 文字を 1 つ削除します。

		var app = require('../app');

	この変更を行うと、行は次のようになります。

		var app = require('./app');

	ファイル (以前の **bin/www**) を必要なアプリケーション ファイルと同じディレクトリに移動しているため、この変更が必要です。この変更を行った後、**server.js** ファイルを保存します。

8.  次のコマンドを使用して、アプリケーションを Windows Azure エミュレーターで実行します。

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## ビューの変更

次に、"Welcome to Express in Azure" というメッセージが表示されるようにビューを変更します
。

1.  次のコマンドを入力して、index.jade ファイルを開きます。

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade は Express アプリケーションで使用される既定のビュー エンジンです。Jade ビュー エンジンの詳細については、[http://jade-lang.com][] を参照してください。

2.  テキストの最後の行に **in Azure** を追加します。

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  ファイルを保存して、メモ帳を終了します。

4.  ブラウザーの表示を最新情報に更新すると、変更を確認できます。

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

アプリケーションのテストが終了したら、**Stop-AzureEmulator** コマンドレットを使用してエミュレーターを停止します。

##Azure にアプリケーションをデプロイする

Azure PowerShell ウィンドウで、**Publish-AzureServiceProject** コマンドレットを使用してアプリケーションをクラウド サービスにデプロイします。

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

デプロイ操作が完了すると、ブラウザーが開き、Web ページが表示されます。

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Node.js Web アプリケーション]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


<!--HONumber=35.2-->
