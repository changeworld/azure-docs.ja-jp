<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Socket.IO を使用する Web サイト" pageTitle="Socket.io を使用する Node.js Web サイト - Azure チュートリアル" metaKeywords="Azure Node.js socket.io チュートリアル, Azure Node.js socket.io, Azure Node.js チュートリアル" description="Azure でホストされる node.js Web サイトで socket.io を使用する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Azure の Web サイトで Socket.IO を使用する Node.js チャット アプリケーションの構築" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />




#Azure の Web サイトで Socket.IO を使用する Node.js チャット アプリケーションの構築

Socket.IO は、node.js サーバーとクライアントの間のリアルタイム通信を提供します。このチュートリアルでは、Azure の Web サイトとして Socket.IO ベースのチャット アプリケーションをホストする手順を説明します。Socket.IO の詳細については、[http://socket.io/][socketio] を参照してください。

<div class="dev-callout"> 
<b>メモ</b>
	<p>このタスクの手順は、Azure の Web サイトに適用されます。クラウド サービスについては、「<a href="http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/app-using-socketio/">Azure クラウド サービスで Socket.IO を使用する Node.js チャット アプリケーションの構築</a>」を参照してください。</p>
</div>

完成したアプリケーションのスクリーンショットは次のようになります。

![チャット アプリケーションを表示しているブラウザー][completed-app]

## <a id="Download"></a>チャットのサンプルのダウンロード

このプロジェクトでは、[Socket.IO GitHub リポジトリ]にあるチャットのサンプルを使用します。次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1. **[複製]** ボタンを使用して、リポジトリのローカル コピーを作成します。**[ZIP]** ボタンを使用してプロジェクトをダウンロードすることもできます。

    ![https://github.com/LearnBoost/socket.io/tree/master/examples/chat が表示され、ZIP のダウンロード アイコンが強調表示されているブラウザー ウィンドウ][chat-example-view]

3. ローカル リポジトリのディレクトリ構造で、**examples\\chat** 
    ディレクトリに移動します。このディレクトリの内容を、**\\node\\chat** 
    などの別のディレクトリにコピーします。

## <a id="Modify"></a>App.js の変更とモジュールのインストール

アプリケーションを Azure に展開する前に、いくつかの点を変更する必要が
あります。次の手順で app.js ファイルを変更します。

1. app.js ファイルをメモ帳などのテキスト エディターで開きます。

2. app.js の先頭にある **Module dependencies** セクションを探して、次のように **sio = require('..//..//lib//socket.io')** を含む行を **sio = require('socket.io')** に変更します。

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated


3. アプリケーションが適切なポートでリッスンするように、メモ帳などの
    エディターで app.js を開き、次のように **3000** を **process.env.PORT** 
    に変更します。

        //app.listen(3000, function () {            //Original
		app.listen(process.env.PORT, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

app.js に対する変更を保存してから、次の手順で必要なモジュールを
インストールします。

1. コマンド ラインで **\\node\\chat** ディレクトリに移動し、このアプリケーションで必要なモジュールを次のコマンドを使用してインストールします。

        npm install

    これによって、package.json ファイルにリストされているモジュールがインストールされます。コマンドが完了すると、次のような出力が表示されます。

    ![npm install コマンドの出力][npm-output]

2. このサンプルはもともと Socket.IO GitHub リポジトリの一部であり、相対
    パスによって Socket.IO ライブラリを直接参照していたため、package.json 
    ファイルでは Socket.IO は参照されていませんでした。そのため、次のコマンド
    を発行してインストールする必要があります。

        npm install socket.io -save

## <a id="Publish"></a>Azure の Web サイトの作成と Git 発行の有効化

Azure の Web サイトを作成し、Web サイトの Git 発行を有効にするには、次の手順に従います。

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料評価版のサイト</a>を参照してください。</p>
</div>

1. コマンド ラインで **\\node\chat** ディレクトリに移動します。次のコマンドを使用して新しい Azure の Web サイトを作成し、Web サイトとローカル ディレクトリの Git リポジトリを有効にします。"azure" という名前の Git リモートも作成されます。

		azure site create mysitename --git

	"mysitename" を一意の Web サイト名に置き換える必要があります。

2. 次のコマンドを使用して、既存のファイルをローカル リポジトリにコミットします。

		git add .
		git commit -m "Initial commit"

3. 次のコマンドで、ファイルを Azure の Web サイト リポジトリにプッシュします。

		git push azure master

	モジュールがサーバーにインポートされると、ステータス メッセージを受信します。この処理が完了すると、アプリケーションが Azure の Web サイトでホストされるようになります。

 	<div class="dev-callout">
	<b>注</b>
	<p>モジュールのインストール中に、"インポートされたプロジェクト ... が見つかりませんでした" というエラーが表示される場合があります。このエラーは無視してかまいません。</p>
	</div>

4. Socket.IO では、WebSocket を使用します。WebSocket は、Azure では既定で有効になりません。Web Socket を有効にするには、次のコマンドを使用します。

		azure site set -w

	Web サイトの名前の入力を求められた場合は、入力します。

	>[WACOM.NOTE]
	>"azure site set -w" コマンドは、Version 0.7.4 以降の Azure クロスプラットフォーム コマンド ライン インターフェイスでのみ機能します。また、Azure 管理ポータルを使用して、WebSocket のサポートを有効にすることもできます。
	>
	>[Azure の管理ポータル](https://manage.windowsazure.com)を使用して WebSocket を有効にするには、Web サイトの [構成] ページを選択し、Web Socket エントリで [オン] を選択して、[保存] をクリックします。
	>	
	>![WebSocket](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. Azure で Web サイトを表示するには、次のコマンドを使用して Web ブラウザーを起動し、ホストされている Web サイトに移動します。

		azure site browse

これで、アプリケーションは Azure で実行されるようになり、
Socket.IO を使用する複数のクライアント間でチャット 
メッセージを中継できます。

<div class="dev-callout">
<strong>注</strong>
<p>わかりやすくするために、このサンプルは同じインスタンスに接続したユーザー間でのチャットに制限されています。つまり、クラウド サービスによって 2 つのワーカー ロール インスタンスが作成された場合、ユーザーは同じワーカー ロール インスタンスに接続された他のユーザーとのみチャットすることができます。複数のロール インスタンスで機能するようにこのアプリケーションを拡張するには、サービス バスなどのテクノロジを使用して、インスタンス間で Socket.IO ストアの状態を共有します。たとえば、<a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub リポジトリ</a>にあるサービス バス キューおよびトピックの使用例を参照してください。</p>
</div>

##次のステップ

このチュートリアルでは、Azure の Web サイトでホストされるチャット アプリケーションを作成する方法を説明しました。このアプリケーションは、Azure クラウド サービスとしてホストすることもできます。そのための作業手順については、「[Azure クラウド サービスで Socket.IO を使用する Node.js チャット アプリケーションの構築][cloudservice]」を参照してください。

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub リポジトリ]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[cloudservice]: /ja-jp/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png

