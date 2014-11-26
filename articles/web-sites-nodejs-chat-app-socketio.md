<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure Website での Socket.IO を使用する Node.js チャット アプリケーションの構築

Socket.IO は、WebSocket を使用して node.js サーバーとクライアントの間のリアルタイム通信を提供します。また、古いブラウザーで動作する他のトランスポート (ロング ポーリングなど) へのフォールバックもサポートします。このチュートリアルでは、Azure Website として Socket.IO ベースのチャット アプリケーションをホストする手順を説明します。Socket.IO の詳細については、[][]<http://socket.io/></a> を参照してください。

> [WACOM.NOTE] このタスクの手順は、Azure Websites に適用されます。Cloud Services については、「[Azure Cloud Service での Socket.IO を使用する Node.js チャット アプリケーションの構築][Azure Cloud Service での Socket.IO を使用する Node.js チャット アプリケーションの構築]」を参照してください。

## <span id="Download"></span></a> チャットのサンプルのダウンロード

このプロジェクトでは、[Socket.IO GitHub リポジトリ][Socket.IO GitHub リポジトリ]にあるチャットのサンプルを使用します。次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1.  Socket.IO プロジェクトの [ZIP または GZ アーカイブ リリース][ZIP または GZ アーカイブ リリース]をダウンロードします (このドキュメントではバージョン 1.0.6 を使用しました)。

2.  アーカイブを展開し、**examples\\chat** ディレクトリを新しい場所にコピーします。
    たとえば、**\\node\\chat** です。

## <span id="Modify"></span></a>App.js の変更とモジュールのインストール

アプリケーションを Azure にデプロイする前に、いくつかの点を変更する必要があります。

1.  **index.js** ファイルの名前を **app.js** に変更します。これにより、Azure はこれが Node.js アプリケーションであることを検出できます。

2.  **app.js** ファイルをメモ帳などのテキスト エディターで開きます。

3.  app.js の先頭にある **Module dependencies** セクションを探して、次のように `var io = require('../..')(server);` を含む行を `var io = require('socket.io')(server);` に変更します。

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

app.js に対する変更を保存してから、次の手順で必要なモジュールをインストールします。

1.  コマンド ラインで **\\node\\chat** ディレクトリに移動し、このアプリケーションで必要なモジュールを次のコマンドを使用してインストールします。

        npm install

    これによって、package.json ファイルにリストされているモジュールがインストールされます。コマンドが完了すると、
    次のような出力が
    表示されます。

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  このサンプルはもともと Socket.IO GitHub リポジトリの一部であり、相対パスによって Socket.IO ライブラリを直接参照していたため、package.json ファイルでは Socket.IO は参照されていませんでした。そのため、次のコマンドを発行してインストールする必要があります。

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] 新しいバージョンの Socket.IO もこの記事の手順で動作する可能性がありますが、テストはバージョン 1.0.6 で行われました。

## <span id="Publish"></span></a>Azure Website の作成

Azure Website を作成し、Git 発行を有効にして、Web サイトの WebSocket サポートを有効にするには、次の手順に従います。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

1.  コマンド ラインで **\\node\\chat** ディレクトリに移動し、次のコマンドを使用して新しい Azure Website を作成し、Web サイトとローカル ディレクトリの Git リポジトリを有効にします。"azure" という名前の Git リモートも作成されます。

        azure site create mysitename --git

    "mysitename" を一意の Web サイト名に置き換える必要があります。

2.  次のコマンドを使用して、既存のファイルをローカル リポジトリにコミットします。

        git add .
        git commit -m "Initial commit"

3.  次のコマンドで、ファイルを Azure Website リポジトリにプッシュします。

        git push azure master

    モジュールがサーバーにインポートされると、ステータス メッセージを受信します。この処理が完了すると、アプリケーションが Azure Website でホストされるようになります。

    > [WACOM.NOTE] モジュールのインストール中に、"インポートされたプロジェクト ... が見つかりませんでした" というエラーが表示される場合があります。このエラーは無視してかまいません。

4.  Socket.IO では、WebSocket を使用します。WebSocket は、Azure では既定で有効になりません。Web Socket を有効にするには、次のコマンドを使用します。

        azure site set -w

    Web サイトの名前の入力を求められた場合は、入力します。

    > [WACOM.NOTE]
    > "azure site set -w" コマンドは、Version 0.7.4 以降の Azure クロスプラットフォーム コマンド ライン インターフェイスでのみ機能します。また、Azure 管理ポータルを使用して、WebSocket のサポートを有効にすることもできます。
    >
    > [Azure 管理ポータル][Azure 管理ポータル]を使用して WebSocket を有効にするには、Web サイトの [構成] ページを選択し、Web Socket エントリで [オン] を選択して、[保存] をクリックします。
    >
    > ![WebSocket][WebSocket]

5.  Azure で Web サイトを表示するには、次のコマンドを使用して Web ブラウザーを起動し、ホストされている Web サイトに移動します。

        azure site browse

これで、アプリケーションは Azure で実行されるようになり、
Socket.IO を使用する複数のクライアント間でチャット メッセージを中継できます。

> [WACOM.NOTE] わかりやすくするために、このサンプルは同じインスタンスに接続したユーザー間でのチャットに制限されています。つまり、クラウド サービスによって 2 つのワーカー ロール インスタンスが作成された場合、ユーザーは同じワーカー ロール インスタンスに接続された他のユーザーとのみチャットすることができます。複数のロール インスタンスで機能するようにこのアプリケーションを拡張するには、サービス バスなどのテクノロジを使用して、インスタンス間で Socket.IO ストアの状態を共有します。たとえば、[Azure SDK for Node.js GitHub リポジトリ][Azure SDK for Node.js GitHub リポジトリ]にある Service Bus キューおよびトピックの使用例を参照してください。

## スケールアウト

**アダプター**を使用してメッセージとイベントを複数のアプリケーション インスタンスに分配することにより、Socket.IO アプリケーションをスケールアウトできます。複数のアダプターを使用できますが、Azure Redis Cache 機能では [socket.io-redis][socket.io-redis] アダプターを簡単に使用できます。

> [WACOM.NOTE] Socket.IO ソリューションのスケールアウトに対する追加要件は、スティッキ セッションのサポートです。Azure Websites では Azure Request Routing によるスティッキ セッションが既定で有効になっています。詳細については、「[Azure Web サイトでのインスタンスのアフィニティ][Azure Web サイトでのインスタンスのアフィニティ]」を参照してください。

### Redis Cache の作成

「[Azure Redis Cache でのキャッシュの作成][Azure Redis Cache でのキャッシュの作成]」の手順を実行して、新しいキャッシュを作成します。

> [WACOM.NOTE] キャッシュの**ホスト名**と**プライマリ キー**を保存します。これらは次の手順で必要になります。

### Redis および socket.io-redis モジュールの追加

1.  コマンド ラインから、**\\node\\chat** ディレクトリに移動し、次のコマンドを実行します。

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] このコマンドで指定されているバージョンは、この記事をテストするときに使用したバージョンです。

2.  **app.js** ファイルを変更し、次の行を `var io = require('socket.io')(server);` の直後に追加します。

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    **redishostname** および **rediskey** は、実際の Redis Cache のホスト名とキーに置き換えます。

    これにより、前の手順で作成した Redis Cache に対する発行とサブスクライブ クライアントが作成されます。その後、クライアントは、Redis Cache を使用してアプリケーション インスタンス間のメッセージおよびイベントの受け渡しを行うように Socket.IO を構成するために、アダプターで使用されます。

    > [WACOM.NOTE] **socket.io-redis** アダプターは Redis と直接通信できますが、現在のバージョン (2014/7/14 時点) は Azure Redis Cache で必要な認証をサポートしていません。したがって、**redis** モジュールを使用して最初の接続が作成された後、クライアントが **socket.io-redis** アダプターに渡されます。
    >
    > Azure Redis Cache はポート 6380 を使用するセキュリティで保護された接続をサポートしますが、この例で使用されているモジュールは、2014/7/14 の時点では、セキュリティで保護された接続をサポートしていません。前記のコードは、セキュリティで保護されていない既定のポート 6380 を使用しています。

3.  変更した **app.js** を保存します。

### 変更のコミットと再デプロイ

**\\node\\chat** ディレクトリのコマンド ラインから、次のコマンドを使用して変更をコミットし、アプリケーションを再デプロイします。

    git add .
    git commit -m "implementing scale out"
    git push azure master

変更をサーバーにプッシュした後は、次のコマンドを使用してサイトを複数のインスタンスに拡大できます。

    azure site scale instances --instances #

**\#** は、作成するインスタンスの数です。

複数のブラウザーまたはコンピューターから Web サイトに接続して、メッセージがすべてのクライアントに正しく送信されることを確認できます。

## <span id="tshooting"></span></a>トラブルシューティング

### 接続制限

Azure Websites は複数の SKU で使用でき、SKU によってサイトで使用できるリソースが決まります。これには、許可される WebSocket 接続の数が含まれます。詳細については、[Web サイトの料金のページ][Web サイトの料金のページ]を参照してください。

### WebSocket を使用してメッセージを送信できない

クライアントのブラウザーがロング ポーリングへのフォールバックを続け、WebSocket を使用しない場合、次のいずれかによって解決する可能性があります。

-   **トランスポートを WebSocket だけに限定してみる**

    Socket.IO がメッセージング トランスポートとして WebSocket を使用するには、サーバーとクライアントの両方が WebSocket をサポートしている必要があります。どちらかがサポートしていない場合、Socket.IO はロング ポーリングなどの別のトランスポートをネゴシエートします。Socket.IO によって使用されるトランスポートの既定のリストは、`websocket, htmlfile, xhr-polling, jsonp-polling` です。**app.js** ファイルの `, nicknames = {};` を含む行の後に次のコードを追加することによって、WebSocket だけを使用するように強制できます。

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] 上記のコードは通信を WebSocket だけに制限するので、WebSocket をサポートしない古いブラウザーは、上記のコードが有効になっている間はサイトに接続できないことに注意してください。

-   **SSL を使用する**

    WebSocket は **Upgrade** ヘッダーなどの使用頻度が低い一部の HTTP ヘッダーに依存します。Web プロキシなど、中間ネットワーク デバイスの中にはこれらのヘッダーを削除するものがあります。この問題を避けるには、WebSocket 接続を SSL 経由で確立できます。

    これを実現する簡単な方法は、Socket.IO を `match origin protocol` に構成することです。この指定は、Socket.IO に、Web ページに対する HTTP/HTTPS 要求を送信するときと同じように WebSocket 通信をセキュリティで保護するように指示します。ブラウザーが HTTPS URL を使用して Web サイトにアクセスすると、それ以降の Socket.IO を使用した WebSocket 通信は SSL によってセキュリティで保護されます。

    この構成が有効になるようにこの例を変更するには、**app.js** ファイルの `, nicknames = {};` を含む行の後に次のコードを追加します。

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **web.config の設定を確認する**

    Node.js アプリケーションをホストする Azure Websites は、**web.config** ファイルを使用して、受け取った要求を Node.js アプリケーションにルーティングします。Node.js アプリケーションで WebSocket が正常に機能するためには、**web.config** に次のエントリが含まれる必要があります。

        <webSocket enabled="false"/>

    これにより IIS WebSocket モジュールが無効になりますが、それには WebSocket の独自の実装が含まれ、それは Socket.IO などの Node.js 固有の WebSocket モジュールと競合します。この行が存在しない場合、または `true` に設定されている場合は、WebSocket トランスポートがアプリケーションで動作しない原因になる可能性があります。

    通常、Node.js アプリケーションには **web.config** ファイルが含まれないので、Azure Websites はデプロイされるときに Node.js アプリケーション用にこのファイルを自動的に生成します。このファイルはサーバー上に自動的に生成されるので、Web サイトでこのファイルを参照するには FTP または FTPS URL を使用する必要があります。Azure 管理ポータルでサイトの FTP および FTPS URL を検索するには、Web サイトを選択して、**[ダッシュボード]** リンクをクリックします。URL は **[概要]** セクションに表示されます。

    > [WACOM.NOTE] **web.config** ファイルが Azure Websites によって生成されるのは、アプリケーションで提供されていない場合だけです。アプリケーション プロジェクトのルートで **web.config** ファイルを提供した場合は、それが Azure Websites によって使用されます。

    エントリが存在しない場合、または値 `true` に設定されている場合は、Node.js アプリケーションのルートに **web.config** を作成し、値 `false` を指定する必要があります。参考として、エントリ ポイントとして **app.js** を使用するアプリケーションの既定の **web.config** を次に示します。

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] アプリケーションで **app.js** 以外のエントリ ポイントを使用している場合は、すべての **app.js** を正しいエントリ ポイントに置き換える必要があります。たとえば、**app.js** を **server.js** に置き換えます。

## 次のステップ

このチュートリアルでは、Azure Website でホストされるチャット アプリケーションを作成する方法を説明しました。このアプリケーションは、Azure クラウド サービスとしてホストすることもできます。そのための作業手順については、「[Azure クラウド サービスで Socket.IO を使用する Node.js チャット アプリケーションの構築][Azure クラウド サービスで Socket.IO を使用する Node.js チャット アプリケーションの構築]」を参照してください。

  []: http://socket.io/
  [Azure Cloud Service での Socket.IO を使用する Node.js チャット アプリケーションの構築]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/app-using-socketio/
  [Socket.IO GitHub リポジトリ]: https://github.com/Automattic/socket.io
  [ZIP または GZ アーカイブ リリース]: https://github.com/Automattic/socket.io/releases
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E
  [Azure 管理ポータル]: https://manage.windowsazure.com
  [WebSocket]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [Azure SDK for Node.js GitHub リポジトリ]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Azure Web サイトでのインスタンスのアフィニティ]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Azure Redis Cache でのキャッシュの作成]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Web サイトの料金のページ]: /ja-jp/pricing/details/web-sites/
  [Azure クラウド サービスで Socket.IO を使用する Node.js チャット アプリケーションの構築]: /ja-jp/develop/nodejs/tutorials/app-using-socketio/
