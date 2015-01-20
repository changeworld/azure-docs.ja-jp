<properties urlDisplayName="App Using Socket.IO" pageTitle="Socket.io を使用する Node.js アプリケーション - Azure チュートリアル" metaKeywords="Azure Node.js socket.io チュートリアル, Azure Node.js socket.io, Azure Node.js チュートリアル" description="Azure でホストされる node.js アプリケーションで socket.io を使用する方法を示すチュートリアル。" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />





# Azure Cloud Services で Socket.IO を使用する Node.js チャット アプリケーションを構築する

Socket.IO は、node.js サーバーとクライアントの間のリアルタイム通信を提供します。このチュートリアルでは、Azure で socket.IO ベースのチャット アプリケーションをホストする手順を説明します。Socket.IO の詳細については、<a href="http://socket.io/">http://socket.io/</a> を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。
	
![A browser window displaying the service hosted on Azure][completed-app]  

## クラウド サービス プロジェクトの作成

次の手順では、Socket.IO アプリケーションをストリーミングするクラウド サービス プロジェクトを作成します。

1. **[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. **c:\node** ディレクトリに移動し、次のコマンドを入力して **chatapp** という名前の新しいソリューションと **WorkerRole1** という名前の worker ロールを作成します。

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	次の応答が表示されます。

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## チャットのサンプルのダウンロード

このプロジェクトでは、[Socket.IO GitHub リポジトリ]にあるチャットのサンプルを使用します。次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1.  **[複製]** ボタンを使用して、リポジトリのローカル コピーを作成します。**[ZIP]** ボタンを使用してプロジェクトをダウンロードすることもできます。

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  ローカル リポジトリのディレクトリ構造で、**examples\\chat**   ディレクトリに移動します。このディレクトリの内容を、先ほど作成した **C:\\node\\chatapp\\WorkerRole1** ディレクトリにコピーします。

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    上記のスクリーンショットの強調表示されている項目は、**examples\chat** ディレクトリからコピーされたファイルです。

4.  **C:\node\chatapp\WorkerRole1** ディレクトリで、**server.js** ファイルを削除し、**app.js** ファイルの名前を **server.js** に変更します。これにより、前に **Add-AzureNodeWorkerRole** コマンドレットで作成された既定の **server.js** ファイルが削除され、チャットのサンプルのアプリケーション ファイルに置き換えられます。

### Server.js の変更とモジュールのインストール

Azure エミュレーターでアプリケーションをテストする前に、いくつかの点を変更する必要があります。次の手順で server.js ファイルを変更します。

1.  server.js ファイルをメモ帳などのテキスト エディターで開きます。

2.  server.js の先頭にある **Module dependencies** セクションを探して、次のように **sio = require('..//..//lib//socket.io')** を含む行を **sio = require('socket.io')** に変更します。

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  アプリケーションが適切なポートでリッスンするように、メモ帳などのエディターで server.js を開き、次の行の **3000** を **process.env.port** に変更します。

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

server.js の変更内容を保存した後、次の手順に従って必要なモジュールをインストールし、Azure エミュレーターでアプリケーションをテストします。

1.  **Azure PowerShell** を使用して、**C:\node\chatapp\WorkerRole1** ディレクトリに移動し、次のコマンドを使用して、このアプリケーションで必要なモジュールをインストールします。

        PS C:\node\chatapp\WorkerRole1> npm install

    これによって、package.json ファイルにリストされているモジュールがインストールされます。コマンドが完了すると、次のような出力が表示されます。 
    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  このサンプルはもともと Socket.IO GitHub リポジトリの一部であり、相対パスによって Socket.IO ライブラリを直接参照していたため、package.json ファイルでは Socket.IO は参照されていませんでした。そのため、次のコマンドを発行してインストールする必要があります。

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### テストとデプロイ

1.  次のコマンドを発行してエミュレーターを起動します。

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  ブラウザー ウィンドウが開いたら、ニックネームを入力して Enter キーを押します。これにより、特定のニックネームでメッセージが投稿されます。マルチユーザー機能をテストするには、同じ URL を使用して新しいブラウザー ウィンドウを開き、別のニックネームを入力します。

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  アプリケーションのテストが終了したら、次のコマンドを発行してエミュレーターを停止します。

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Azure にアプリケーションをデプロイするには、**Publish-AzureServiceProject** コマンドレットを使用します。次に例を示します。

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	<div class="dev-callout">
	<strong>注</strong>
	<p>必ず一意の名前を使用してください。一意でない場合は発行処理が失敗します。デプロイが完了すると、ブラウザーが開き、デプロイされたサービスに移動します。</p>
	<p>指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。「<a href="https://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/">Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ</a>」セクションの「<b>Azure へのアプリケーションのデプロイ</b>」を参照してください。</p>
	</div>

    ![A browser window displaying the service hosted on Azure][completed-app]

	<div class="dev-callout">
	<strong>注</strong>
	<p>指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。「<a href="https://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/">Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ</a>」セクションの「<b>Azure へのアプリケーションのデプロイ</b>」を参照してください。</p>
	</div>

アプリケーションが Azure で実行されており、
Socket.IO を使用して異なるクライアント間でチャット メッセージをリレーできます。

<div class="dev-callout">
<strong>注</strong>
<p>わかりやすくするために、このサンプルは同じインスタンスに接続したユーザー間でのチャットに制限されています。つまり、クラウド サービスによって 2 つのワーカー ロール インスタンスが作成された場合、ユーザーは同じワーカー ロール インスタンスに接続された他のユーザーとのみチャットすることができます。複数のロール インスタンスで機能するようにこのアプリケーションを拡張するには、サービス バスなどのテクノロジを使用して、インスタンス間で Socket.IO ストアの状態を共有します。たとえば、<a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub リポジトリ</a>にある Service Bus キューおよびトピックの使用例を参照してください。</p>
</div>

##次のステップ

このチュートリアルでは、Azure Cloud Services でホストされる基本的なチャット アプリケーションを作成する方法を説明しました。Azure Web サイトでこのアプリケーションをホストする方法については、「[Azure Web サイトで Socket.IO を使用する Node.js チャット アプリケーションを構築する][chatwebsite]」を参照してください。

  [chatwebsite]: /ja-jp/develop/nodejs/tutorials/website-using-socketio/

  [Azure の SLA]: http://www.windowsazure.com/ja-jp/support/sla/
  [Azure SDK for Node.js GitHub リポジトリ]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/ja-jp/develop/nodejs/
  [Node.js Web アプリケーション]: https://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub リポジトリ]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure に関する考慮事項]: #windowsazureconsiderations
  [ワーカー ロールでのチャット サンプルのホスト]: #hostingthechatexampleinawebrole
  [概要と次のステップ]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [チャット サンプル]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=35.2-->
