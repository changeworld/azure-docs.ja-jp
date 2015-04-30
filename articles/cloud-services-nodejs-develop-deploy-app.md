<properties 
	pageTitle="Node.js 概要ガイド - Azure チュートリアル" 
	description="簡単な Node.js Web アプリケーションを作成して、Azure のクラウド サービスにデプロイする方法について説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

このガイドでは、Azure のクラウド サービスで実行される簡単な Node.js アプリケーションを作成します。クラウド サービスは、Azure のスケーラブルなクラウド アプリケーションの構成要素です。クラウド サービスによって、アプリケーションのフロントエンド コンポーネントとバックエンド コンポーネントの分離および独立した管理とスケールアウトを実現できます。クラウド サービスは、各ロールを信頼性の高い方法でホストするための堅牢な専用仮想マシンを提供します。

クラウド サービスの詳細と Azure の Web サイトおよび仮想マシンとの違いについては、「[Azure の Web サイト、クラウド サービス、および仮想マシンの比較](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/)」をご覧ください。

>[AZURE.TIP] 単純な Web サイトを構築する場合実現するシナリオが単純な Web サイトのフロントエンドにのみ関係している場合は、<a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">軽量の Azure の Web サイトを使用することを検討してください。</a>Web サイトの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。


このチュートリアルでは、Web ロールでホストされる単純な Web アプリケーションを作成します。コンピューティング エミュレーターを使用してアプリケーションをローケルでテストし、PowerShell コマンド ライン ツールを使用してアプリケーションをデプロイします。

このアプリケーションは簡単な "hello world" アプリケーションです。

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Hello World ページを表示するブラウザー ウィンドウ。この URL は、ページが Azure でホストされていることを示します。">
</p>

## 前提条件

> [AZURE.NOTE] このチュートリアルは、Windows 上の Azure PowerShell を使用します。

- Azure SDK for Node.js をインストールします:<a href="http://go.microsoft.com/fwlink/?LinkId=254279">Windows インストーラー</a> 

- [Azure Powershell](install-configure-powershell.md).


## Azure クラウド サービス プロジェクトの作成

新しい Azure クラウド サービス プロジェクトおよび基本的な Node.js スキャフォールディングを作成するには、次のタスクを実行します。


1. **[Azure PowerShell]** を管理者として実行します。**[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。

2.  次の PowerShell コマンドレットを入力してプロジェクトを作成します。

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	**New-AzureServiceProject** コマンドレットは、クラウド サービスに Node.js アプリケーションを発行するための基本的な構造を生成します。これには、Azure への発行に必要な構成ファイルが含まれています。また、このコマンドレットにより、作業ディレクトリがこのサービス用のディレクトリに変更されます。

	このコマンドレットによって作成されるファイルは、次のとおりです。

	-   **ServiceConfiguration.Cloud.cscfg**、 **ServiceConfiguration.Local.cscfg** 、および **ServiceDefinition.csdef**:アプリケーション発行に必要な Azure 専用ファイル。 詳細については、「[Overview of Creating a Hosted Service for Windows Azure (Windows Azure 対応のホステッド サービスの作成の概要)][]」を参照してください。

	-   **deploymentSettings.json**: には、Windows Azure PowerShell デプロイ コマンドレットによって使用されるローカル設定が格納されます。

4.  次のコマンドを入力して、新しい Web ロールを追加します。

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	**Add-azurenodewebrole**コマンドレットは、基本的な Node.js アプリケーションを作成します。**.csfg** ファイルと **.csdef** ファイルも変更され、新しいロールの構成エントリが追加されます。

	> [AZURE.NOTE] ロール名を指定しない場合は、既定の名前が使用されます。名前は、`Add-AzureNodeWebRole MyRole` のように、最初のコマンドレット パラメーターとして指定できます。


Node.js アプリケーションは、**server.js** ファイルで定義され、このファイルは Web ロールのディレクトリにあります (既定では、**WebRole1**)。次にコードを示します。

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

このコードは、クラウド環境に割り当てられたポート番号を使用する点を除いて、基本的に [nodejs.org][] Web サイトの "Hello World" のサンプルと同じです。


## エミュレーターでのアプリケーションのローカル実行

Azure SDK によってインストールされるツールの 1 つに、Azure コンピューティング エミュレーターがあります。このツールを使用すると、アプリケーションをローカルでテストできます。このクラウドにデプロイされたときのアプリケーションの実行環境がシミュレートされます。 

1.  次のAzure PowerShell コマンドレットを入力してエミュレーターでサービスを実行します。

        Start-AzureEmulator -Launch

	**-Launch** パラメーターを使用すると、エミュレーターでWeb ロールを実行するときに、ブラウザー ウィンドウが自動的に開きます以下のスクリーンショットに示すように、ブラウザー ウィンドウに "Hello World" という文字が表示されます。 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  コンピューティング エミュレーターを停止するには、**Stop-AzureEmulator** コマンドレットを使用します。
	
		Stop-AzureEmulator

## Azure にアプリケーションをデプロイする

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Azure 発行設定のダウンロード

アプリケーションを Azure に展開するには、まず Azure サブスクリプションの発行設定をダウンロードする必要があります。 

1.  次の Azure PowerShell コマンドレットを実行します。

        Get-AzurePublishSettingsFile

	ブラウザーで、発行設定のダウンロード ページが表示されます。Microsoft アカウントによるログインを求められる場合があります。その場合は、Azure サブスクリプションに関連付けられたアカウントを使用します。

	ダウンロードしたプロファイルを、簡単にアクセスできるファイルの保存場所に保存します。

2.  次のコマンドレットを実行して、ダウンロードした発行プロファイルをインポートします。

        Import-AzurePublishSettingsFile [ファイルへのパス]


	> [AZURE.NOTE] 発行設定をインポートしたら、ダウンロードした .publishSettings ファイルには他のユーザーがアカウントにアクセスできる情報が含まれているので、削除することを検討してください。
    

### アプリケーションの発行

発行するには、**Publish-AzureServiceProject** コマンドレットを次のように実行します。

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** は、この展開に使用する名前を指定します。必ず一意の名前を使用してください。一意でない場合は発行が失敗します。

- **-Location** は、アプリケーションがホストされるデータセンターを指定します。利用可能なデータセンターの一覧を表示するには、**Get-AzureLocation** コマンドレットを使用します。

- **-Launch** は、展開の完了後に、ブラウザー ウィンドウの起動とホステッド サービスへの移動を行います。

発行が成功すると、次のような応答が表示されます。

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> 初めてアプリケーションを発行する場合、アプリケーションが展開され、利用可能になるまでに、5 ～ 7 分かかる場合があります。

展開が完了すると、ブラウザー ウィンドウが開き、クラウド サービスに移動します。


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

これで、アプリケーションが Azure 上で実行されました。

**Publish-AzureServiceProject** コマンドレットによって行われる処理は次のとおりです。

1.  展開するためのパッケージを作成します。このパッケージには、アプリケーション フォルダー内のすべてのファイルが含まれます。

2.  **ストレージ アカウント**が存在しない場合は、新たに作成します。Azure ストレージ アカウントは、展開中にアプリケーション パッケージを格納するために使用されます。展開が完了したら、ストレージ アカウントを削除してかまいません。

3.  **クラウド サービス**が存在しない場合は、新たに作成します。**クラウド サービス**は、Azure に展開したアプリケーションがホストされるコンテナーです。詳細については、「[Azure 対応のホステッド サービスの作成の概要][]」をご覧ください。

4.  展開パッケージを Azure に発行します。



## アプリケーションの停止と削除

アプリケーションを展開した後、余分なコストを回避するためにアプリケーションを無効にすることができます。Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

1.  Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスの展開を停止します。

        Stop-AzureService

	サービスの停止には、数分間かかる場合があります。サービスが停止すると、停止したことを知らせるメッセージが表示されます。

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  サービスを削除するには、次のコマンドレットを呼び出します。

        Remove-AzureService

	確認を求めるメッセージが表示されたら、「**Y**」と入力して、サービスを削除します。

	サービスの削除には、数分間かかる場合があります。サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] サービスを削除しても、サービスが最初に発行されたときに作成されたストレージ アカウントは削除されず、使用したストレージに対して引き続き課金されます。ストレージ アカウントの削除の詳細については、「[Azure サブスクリプションからストレージ アカウントを削除する方法](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx)」をご覧ください。



[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[helloworld フォルダーのディレクトリ一覧です。]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Azure 対応のホステッド サービスの作成の概要]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[WebRole1 フォルダーのディレクトリ一覧]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Overview of Creating a Hosted Service for Windows Azure (Windows Azure 対応のホステッド サービスの作成の概要)]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[タスク バーから Windows Azure エミュレーターを右クリックすると表示されるメニュー。]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[無料評価版のリンクをハイライト表示して http://www.windowsazure.com/ を表示するブラウザー ウィンドウ]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[LiveID サインイン ページを表示するブラウザー ウィンドウ]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png

[Publish-AzureService コマンドの完全な状態出力]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Azure サブスクリプションからストレージ アカウントを削除する方法]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=52-->