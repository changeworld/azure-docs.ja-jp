<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="App Using Socket.IO" pageTitle="Node.js application using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js application hosted on Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure Cloud Services で Socket.IO を使用する Node.js チャット アプリケーションを構築する

Socket.IO は、node.js サーバーとクライアントの間のリアルタイム通信を提供します。このチュートリアルでは、Azure でsocket.IO ベースのチャット アプリケーションをホストする手順を説明します。Socket.IO の詳細については、<http://socket.io/> を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure でホストされるサービスを表示しているブラウザー ウィンドウ][Azure でホストされるサービスを表示しているブラウザー ウィンドウ]

## クラウド サービス プロジェクトの作成

次の手順では、Socket.IO アプリケーションをストリーミングするクラウド サービス プロジェクトを作成します。

1.  **[スタート] メニュー**または**スタート画面**で、**Azure PowerShell** を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

    ![Azure PowerShell アイコン][Azure PowerShell アイコン]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  **c:\\node** ディレクトリに移動し、次のコマンドを入力して **chatapp** という名前の新しいソリューションと **WorkerRole1** という名前の worker ロールを作成します。

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    次の応答が表示されます。

    ![new-azureservice および add-azurenodeworkerrole コマンドレットの出力][new-azureservice および add-azurenodeworkerrole コマンドレットの出力]

## チャットのサンプルのダウンロード

このプロジェクトでは、[Socket.IO GitHub リポジトリ][Socket.IO GitHub リポジトリ]にあるチャットのサンプルを使用します。次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1.  **[複製]** ボタンを使用して、リポジトリのローカル コピーを作成します。**[ZIP]** ボタンを使用してプロジェクトをダウンロードすることもできます。

    ![https://github.com/LearnBoost/socket.io/tree/master/examples/chat が表示され、ZIP のダウンロード アイコンが強調表示されているブラウザー ウィンドウ][https://github.com/LearnBoost/socket.io/tree/master/examples/chat が表示され、ZIP のダウンロード アイコンが強調表示されているブラウザー ウィンドウ]

2.  ローカル リポジトリのディレクトリ構造で、**examples\\chat** ディレクトリに移動します。このディレクトリの内容を、先ほど作成した**C:\\node\\chatapp\\WorkerRole1** ディレクトリにコピーします。

    ![アーカイブから展開された examples\chat ディレクトリの内容を表示しているエクスプローラー][chat-contents]

    上記のスクリーンショットの強調表示されている項目は、**examples\\chat** ディレクトリからコピーされたファイルです。

3.  **C:\\node\\chatapp\\WorkerRole1** ディレクトリで、**server.js** ファイルを削除し、**app.js** ファイルの名前を **server.js** に変更します。これにより、前に **Add-AzureNodeWorkerRole** コマンドレットで作成された既定の **server.js** ファイルが削除され、チャットのサンプルのアプリケーション ファイルに置き換えられます。

### Server.js の変更とモジュールのインストール

Azure エミュレーターでアプリケーションをテストする前に、いくつかの点を変更する必要があります。次の手順でserver.js ファイルを変更します。

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

1.  **Azure PowerShell** を使用して、**C:\\node\\chatapp\\WorkerRole1** ディレクトリに移動し、次のコマンドを使用して、このアプリケーションで必要なモジュールをインストールします。

        PS C:\node\chatapp\WorkerRole1> npm install

    これによって、package.json ファイルにリストされているモジュールがインストールされます。コマンドが完了すると、次のような出力が表示されます。

    ![npm install コマンドの出力][npm install コマンドの出力]

2.  このサンプルはもともと Socket.IO GitHub リポジトリの一部であり、相対パスによって Socket.IO ライブラリを直接参照していたため、package.json ファイルでは Socket.IO は参照されていませんでした。そのため、次のコマンドを発行してインストールする必要があります。

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### テストとデプロイ

1.  次のコマンドを発行してエミュレーターを起動します。

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  ブラウザー ウィンドウが開いたら、ニックネームを入力して Enter キーを押します。これにより、特定のニックネームでメッセージが投稿されます。マルチユーザー機能をテストするには、同じ URL を使用して新しいブラウザー ウィンドウを開き、別のニックネームを入力します。

    ![User1 と User2 からのチャット メッセージを表示している 2 つのブラウザー ウィンドウ][User1 と User2 からのチャット メッセージを表示している 2 つのブラウザー ウィンドウ]

3.  アプリケーションのテストが終了したら、次のコマンドを発行してエミュレーターを停止します。

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Azure にアプリケーションをデプロイするには、**Publish-AzureServiceProject** コマンドレットを使用します。次に例を示します。

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">
<strong>注</strong>
<p>必ず一意の名前を使用してください。一意でない場合は発行処理が失敗します。デプロイが完了すると、ブラウザーが開き、デプロイされたサービスに移動します。</p>
<p>指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。「<a href="https://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/">Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ</a>」の「<b>Azure へのアプリケーションのデプロイ</b>」を参照してください。</p>
</div>

    ![Azure でホストされるサービスを表示しているブラウザー ウィンドウ][Azure でホストされるサービスを表示しているブラウザー ウィンドウ]

    <div class="dev-callout">
<strong>注</strong>
<p>指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。「<a href="https://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/">Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ</a>」の「<b>Azure へのアプリケーションのデプロイ</b>」を参照してください。</p>
</div>

これで、アプリケーションは Azure で実行されるようになり、Socket.IO を使用する複数のクライアント間でチャット メッセージを中継できます。

<div class="dev-callout">
<strong>注</strong>
<p>わかりやすくするために、このサンプルは同じインスタンスに接続したユーザー間でのチャットに制限されています。つまり、クラウド サービスによって 2 つのワーカー ロール インスタンスが作成された場合、ユーザーは同じワーカー ロール インスタンスに接続された他のユーザーとのみチャットすることができます。複数のロール インスタンスで機能するようにこのアプリケーションを拡張するには、サービス バスなどのテクノロジを使用して、インスタンス間で Socket.IO ストアの状態を共有します。たとえば、<a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub リポジトリ</a>にある Service Bus キューおよびトピックの使用例を参照してください。</p>
</div>

## 次のステップ

このチュートリアルでは、Azure Cloud Services でホストされる基本的なチャット アプリケーションを作成する方法を説明しました。Azure Websites でこのアプリケーションをホストする方法については、「[Azure Websites で Socket.IO を使用する Node.js チャット アプリケーションを構築する][Azure Websites で Socket.IO を使用する Node.js チャット アプリケーションを構築する]」を参照してください。

  [Azure でホストされるサービスを表示しているブラウザー ウィンドウ]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure PowerShell アイコン]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md
  [new-azureservice および add-azurenodeworkerrole コマンドレットの出力]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [Socket.IO GitHub リポジトリ]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [https://github.com/LearnBoost/socket.io/tree/master/examples/chat が表示され、ZIP のダウンロード アイコンが強調表示されているブラウザー ウィンドウ]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [アーカイブから展開された examples\\chat ディレクトリの内容を表示しているエクスプローラー]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [npm install コマンドの出力]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [User1 と User2 からのチャット メッセージを表示している 2 つのブラウザー ウィンドウ]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Azure Websites で Socket.IO を使用する Node.js チャット アプリケーションを構築する]: /ja-jp/develop/nodejs/tutorials/website-using-socketio/
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
