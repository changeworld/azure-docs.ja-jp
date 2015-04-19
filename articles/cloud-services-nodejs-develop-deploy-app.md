<properties 
	pageTitle="Node.js 概要ガイド - Azure チュートリアル" 
	description="シンプルな Node.js Web アプリケーションの開発および Azure へのデプロイに役立つエンド ツー エンドのチュートリアルです。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" c
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ

このガイドでは、Azure のクラウド サービスで実行される簡単な Node.js アプリケーションを作成します 
。クラウド サービスは、 
Azure のスケーラブルなクラウド アプリケーションの構成要素です。クラウド サービスによって、
アプリケーションのフロントエンド コンポーネントとバックエンド コンポーネントの分離および独立した管理とスケールアウトを実現できます。  クラウド サービス 
は、各ロールを信頼性の高い方法でホストするための堅牢な専用仮想マシンを提供します。

クラウド サービスの詳細と Azure の Web サイトおよび仮想マシンとの違いについては、「[Azure の Web サイト、クラウド サービス、および仮想マシンの比較](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/)」を参照してください。

>[AZURE.TIP] 単純な Web サイトを構築する場合実現するシナリオが単純な Web サイトのフロントエンドにのみ関係している場合は、 <a href="/ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/">軽量の Azure の Web サイトを使用することを検討してください。</a> Web サイトの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。


このチュートリアルでは、Web ロールでホストされる単純な Web アプリケーションを作成します。コンピューティング エミュレーターを
使用してアプリケーションをローカルでテストし、PowerShell コマンド ライン ツールを使用してアプリケーションをデプロイします
。

完成したアプリケーションのスクリーンショットは次のようになります。

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.">
</p>



## 新しいノード アプリケーションの作成

新しい Azure クラウド サービス プロジェクトおよび基本的な Node.js スキャフォールディングを作成するには、次のタスクを実行します。

1. **[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  C ドライブに新しい **node** ディレクトリを作成し、c:\\node ディレクトリに移動します。
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  次のコマンドレットを入力し、新しいソリューションを作成します。

        PS C:\node> New-AzureServiceProject helloworld

    	次の応答が表示されます。

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	**New-AzureServiceProject** コマンドレットは、クラウド サービスに発行される新しい Azure ノード アプリケーションを作成するための基本的な構造を生成します。これには、Azure への発行に必要な構成ファイルが含まれています。また、このコマンドレットにより、作業ディレクトリがこのサービス用のディレクトリに変更されます。

	**New-AzureServiceProject** コマンドレットによって作成されるファイルは、次のとおりです。

	-   **ServiceConfiguration.Cloud.cscfg**、
        **ServiceConfiguration.Local.cscfg** および **ServiceDefinition.csdef** は、
        アプリケーションの発行に必要な Azure 固有のファイルです
        。
		
	これらのファイルの詳細については、「
        [Azure 対応のホステッド サービスの作成の概要][]」を参照してください。

	-   **deploymentSettings.json** には、
        Azure PowerShell デプロイ コマンドレットによって使用されるローカルの設定が格納されます。

4.  次のコマンドを入力して、新しい Web ロールを追加します。
    **Add-AzureNodeWebRole コマンドレットを使用します。**

        PS C:\node\helloworld> Add-AzureNodeWebRole

	次の応答が表示されます。

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	**Add-AzureNodeWebRole** コマンドレットにより、アプリケーション用の新しいディレクトリが作成され、基本的な Node.js アプリケーションのスキャフォールディングが生成されます。前の手順で作成した **ServiceConfiguration.Cloud.csfg**、**ServiceConfiguration.Local.csfg**、および **ServiceDefinition.csdef** ファイルも変更され、新しいロールの構成エントリが追加されます。

	> [AZURE.NOTE] 既定では、ロール名を指定しない場合、ロールが作成されます。名前は、**Add-AzureNodeWebRole** の最初のパラメーターとして指定できます。たとえば、`Add-AzureNodeWebRole MyRole` のように指定します。

5.  次のコマンドを使用して、**WebRole1** ディレクトリに移動し、**server.js** ファイルをメモ帳で開きます。 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	この **server.js** ファイルは **Add-AzureNodeWebRole** コマンドレットで作成されたもので、次のスターター コードが含まれています。このコードは、[nodejs.org][] Web サイトにある "Hello World" サンプルに似ています。相違点は次のとおりです。

   	-   クラウド環境によって割り当てられた適切なポートをアプリケーションで見つけることができるように、 
        ポートが変更されています。
   	-   コンソール ログが削除されています。

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## エミュレーターでのアプリケーションのローカル実行

Azure SDK によってインストールされるツールの 1 つに、Azure コンピューティング エミュレーターがあります。
このツールを使用すると、アプリケーションをローカルでテストできます。コンピューティング エミュレーターでは、
クラウドにデプロイされたときのアプリケーションの実行環境が
シミュレートされます。エミュレーターでアプリケーションをテストするには、次のステップを実行します。

1.  メモ帳を閉じ、Windows PowerShell ウィンドウに切り替えます。
  	次のコマンドレットを入力してエミュレーターでサービスを実行します。

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	**-launch** パラメーターを指定すると、エミュレーターでのアプリケーションの実行後、ツールによってブラウザー ウィンドウが自動的に開かれ、アプリケーションが表示されます。以下のスクリーンショットに示すように、ブラウザーが開いて "Hello World" という文字列が表示されます。これは、サービスがコンピューティング エミュレーターで実行され、正常に動作していることを表しています。

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  コンピューティング エミュレーターを停止するには、**Stop-AzureEmulator** コマンドを使用します。
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Azure へのアプリケーションのデプロイ

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]



### <a id="download_publishing_settings"></a>Azure 発行設定のダウンロード

アプリケーションを Azure に展開するには、まず Azure サブスクリプションの発行設定をダウンロードする必要があります。次のステップでは、このプロセスを説明します。

1.  Windows PowerShell ウィンドウから次のコマンドレットを実行して、ダウンロード ページを起動します。

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	ブラウザーで、発行設定のダウンロード ページが表示されます。Microsoft アカウントによるログインを求められる場合があります。その場合は、Azure サブスクリプションに関連付けられたアカウントを使用します。

	ダウンロードしたプロファイルを、簡単にアクセスできるファイルの保存場所に保存します。

2.  Azure PowerShell ウィンドウで次のコマンドレットを使用して、ダウンロードした Azure 発行プロファイルを使用するように Windows PowerShell for Node.js コマンドレットを構成します。

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] 発行設定をインポートした後、ダウンロードした .publishSettings ファイルには他のユーザーがアカウントにアクセスするために使用できる情報が含まれているので、削除することを検討してください。
    

### アプリケーションの発行

1.  以下に示すとおり、**Publish-AzureServiceProject** コマンドレットを使用してアプリケーションを発行します
    。

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- **servicename** パラメーターで、この展開に使用する名前を指定します。必ず一意の名前を使用してください。一意でない場合は発行が失敗します。

	- **location** パラメーターは、アプリケーションがホストされるデータセンターを指定します。利用可能なデータセンターの一覧を表示するには、**Get-AzureLocation** コマンドレットを使用します。

	- **launch** パラメーターは、展開の完了後に、ブラウザーの起動とホステッド サービスへの移動を行います。

	発行が成功すると、次のような応答が表示されます。

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	**Publish-AzureServiceProject** コマンドレットによって行われる処理は次のとおりです。

1.  Azure に展開されるパッケージを作成します。このパッケージには、node.js アプリケーション フォルダー内のすべてのファイルが含まれます。

2.  **ストレージ アカウント**が存在しない場合は、新たに作成します。Azure ストレージ アカウントは、展開中にアプリケーション パッケージを格納するために使用されます。展開が完了したら、ストレージ アカウントを削除してかまいません。

3.  **クラウド サービス**が存在しない場合は、新たに作成します。**クラウド サービス**は、Azure に展開したアプリケーションがホストされるコンテナーです。詳細については、「[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)][]」を参照してください。

4.  展開パッケージを Azure に発行します。


	> [AZURE.NOTE]
	> 初めてアプリケーションを発行する場合、アプリケーションが展開され、利用可能になるまでに、5 ～ 7 分かかる場合があります。

	展開が完了すると、ブラウザー ウィンドウが開き、クラウド サービスに移動します。


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	これで、アプリケーションが Azure 上で実行されました。


## アプリケーションの停止と削除

アプリケーションを展開した後、余分なコストを回避するためにアプリケーションを無効にすることができます。Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

1.  Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスの展開を停止します。

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	サービスの停止には、数分間かかる場合があります。サービスが停止すると、停止したことを知らせるメッセージが表示されます。

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  サービスを削除するには、次のコマンドレットを呼び出します。

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	確認を求めるメッセージが表示されたら、「**Y**」と入力して、サービスを削除します。

	サービスの削除には、数分間かかる場合があります。サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] サービスを削除しても、サービスが最初に発行されたときに作成されたストレージ アカウントは削除されず、使用したストレージに対して引き続き課金されます。ストレージ アカウントの削除の詳細については、「[Azure サブスクリプションからストレージ アカウントを削除する方法](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx)」を参照してください。


[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Azure サブスクリプションからストレージ アカウントを削除する方法]: https://www.windowsazure.com/ja-jp/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=45--> 
