<properties urlDisplayName="Enable SSL worker role" pageTitle="クラウド サービス (Node.js) ワーカー ロール向けの SSL の構成" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Azure ワーカー ロールで Node.js アプリケーションの SSL を構成する" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Azure ワーカー ロールで Node.js アプリケーションの SSL を構成する

Secure Socket Layer (SSL) の暗号化は、
インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法です。この一般的なタスクでは、
worker ロールで Azure Cloud Services としてホストされる Node.js アプリケーションの HTTPS エンドポイントを指定する方法について説明します。

<div class="dev-callout">

**注**
この記事で説明する手順は、worker ロールで Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。

</div>

このタスクの手順は次のとおりです。

-   [手順 1.Node.js サービスを作成してクラウドにサービスを発行する][手順 1.Node.js サービスを作成してクラウドにサービスを発行する]
-   [手順 2.SSL 証明書を取得する][手順 2.SSL 証明書を取得する]
-   [手順 3.SSL 証明書を使用するようにアプリケーションを変更する][手順 3.SSL 証明書を使用するようにアプリケーションを変更する]
-   [手順 4.サービス定義ファイルを変更する][手順 4.サービス定義ファイルを変更する]
-   [手順 5.HTTPS を使用してロール インスタンスに接続する][手順 5.HTTPS を使用してロール インスタンスに接続する]

## <a name="step1"> </a>手順 1.Node.js サービスを作成してクラウドにサービスを発行する

Azure PowerShell を使用して、
以下の手順に従って簡単な Node.js "hello world" サービスを作成できます。

1.  **[スタート] メニュー**または**スタート画面**で、**Azure PowerShell** を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

    ![Azure PowerShell アイコン][Azure PowerShell アイコン]

2.  **New-AzureServiceProject** コマンドレットを使用して、新しいサービスを作成します。

    ![][0]

3.  **Add-AzureNodeWorkerRole** コマンドレットを使用して、サービスに worker ロールを追加します。

    ![][1]

4.  **Publish-AzureServiceProject** コマンドレットを使用して、サービスをクラウドに発行します。

    ![][2]

    <div class="dev-callout">

    **注**
    以前に Azure サブスクリプションの発行設定をインポートしていない場合、発行しようとするとエラーが出力されます。サブスクリプションの発行設定のダウンロードとインポートについては、[Azure PowerShell for Node.js の使用方法][Azure PowerShell for Node.js の使用方法]に関するページを参照してください。

    </div>

**Publish-AzureServiceProject** コマンドレットから返される**作成された Web サイトの URL** の値には、ホステッド アプリケーションの完全修飾ドメイン名が含まれます。この特定の完全修飾ドメイン名について SSL 証明書を取得し、Azure に展開する必要があります。

## <a name="step2"> </a>手順 2.SSL 証明書を取得する

アプリケーションの SSL を構成するには、
最初に、セキュリティ保護のための証明書を発行する信頼されたサード パーティである、
証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。まだ SSL 証明書がない場合は、
SSL 証明書を販売する会社から
取得する必要があります。

証明書は、Azure における SSL 証明書の
次の要件を満たす必要があります。

-   証明書は秘密キーを含む必要があります。
-   証明書はキー交換のために作成される必要があります (**.pfx** ファイル)。
-   証明書のサブジェクト名はクラウド サービスへのアクセスに使用されるドメインと
    一致する必要があります。cloudapp.net ドメインの SSL 証明書を取得することはできないため、
    証明書のサブジェクト名を、アプリケーションへのアクセスに使用されるカスタム ドメイン名と
    一致させる必要があります。たとえば、「**mysecuresite.cloudapp.net**」のように入力します。
-   証明書では、2048 ビット以上の暗号化を使用する必要があります。

証明書が含まれている **.pfx** ファイルは、次に示す手順でサービス プロジェクトに追加され、Azure に展開されます。

## <a name="step3"> </a>手順 3.SSL 証明書を使用するようにアプリケーションを変更する

Node.js アプリケーションを worker ロールにデプロイすると、サーバー証明書と SSL 接続は Node.exe によって管理されます。SSL トラフィックを処理するには、"http" ではなく "https" モジュールを使用する必要があります。次の手順を実行して、SSL 証明書をプロジェクトに追加し、この証明書を使用するようにアプリケーションを変更します。

1.  証明機関 (CA) から提供された **.pfx** ファイルを、アプリケーションが格納されているディレクトリに保存します。たとえば、この記事で使用されているアプリケーションが格納されているディレクトリは **c:\\node\\securesite\\workerrole1** です。

2.  Notepad.exe を使用して **c:\\node\\securesite\\workerrole1\\server.js** ファイルを開き、ファイルの内容を次の内容に置き換えます。

        var https = require('https');
        var fs = require('fs');

        var options = {
            pfx: fs.readFileSync('certificate.pfx'),
            passphrase: "password"
        };
        var port = process.env.PORT || 8000;
        https.createServer(options, function (req, res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello World\n');
        }).listen(port);

    <div class="dev-callout">

    **注**
    'certificate.pfx' を証明書ファイルの名前に、"password" を証明書ファイルのパスワード (ある場合) に置き換える必要があります。

    </div>

3.  **server.js** ファイルを保存します。

**server.js** ファイルをこのように変更すると、アプリケーションを Azure に展開したときに、アプリケーションはポート 443 (SSL 通信用の標準ポート) で通信をリッスンするようになります。**.pfx** ファイルは、このトランスポートで SSL 通信を実装するために使用されます。

## <a name="step4"> </a>手順 4.サービス定義ファイルを変更する

これで、アプリケーションはポート 443 でリッスンするようになったので、このポートでの通信を許可するようにサービス定義も変更する必要があります。

1.  サービス ディレクトリで、サービス定義ファイル
    (**ServiceDefinition.csdef**) を開き、**Endpoints** セクションの http **InputEndpoint** 要素を更新して、ポート 443 での通信を有効にします。

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    この変更を行った後、**ServiceDefinition.csdef** ファイルを保存します。

2.  もう一度サービスを発行して、クラウド内の構成を最新の情報に
    更新します。Azure PowerShell プロンプトで、
    サービス ディレクトリから「**Publish-AzureServiceProject**」と入力します。

## <a name="step5"> </a>手順 5.HTTPS を使用してロール インスタンスに接続する

Azure でデプロイを実行できるようになったため、
HTTPS を使用して接続できます。

1.  管理ポータルで、目的のクラウド サービスを選択し、**[ダッシュボード]** をクリックします。

2.  下へスクロールし、**[サイトの URL]** として表示されているリンクをクリックします。

    ![サイトの URL][サイトの URL]

    <div class="dev-callout">

    **注**
    ポータルに表示されるサイトの URL で HTTPS が指定されていない場合は、ブラウザーで HTTP の代わりに HTTPS を使用して手動で URL を入力する必要があります。

    </div>

3.  新しいブラウザーが開いて、Web サイトが表示されます。

    ブラウザーには、HTTPS 接続を使用していることを示す
    錠前のアイコンが表示されます。これは、アプリケーションの SSL が
    正しく構成されていることも表します。

    ![][3]

## その他のリソース

[証明書をサービスに関連付ける方法][証明書をサービスに関連付ける方法]

[Azure Web ロールで Node.js アプリケーションの SSL を構成する][Azure Web ロールで Node.js アプリケーションの SSL を構成する]

[HTTPS エンドポイント上での SSL 証明書の構成方法][HTTPS エンドポイント上での SSL 証明書の構成方法]

  [手順 1.Node.js サービスを作成してクラウドにサービスを発行する]: #step1
  [手順 2.SSL 証明書を取得する]: #step2
  [手順 3.SSL 証明書を使用するようにアプリケーションを変更する]: #step3
  [手順 4.サービス定義ファイルを変更する]: #step4
  [手順 5.HTTPS を使用してロール インスタンスに接続する]: #step5
  [Azure PowerShell アイコン]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  [0]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [Azure PowerShell for Node.js の使用方法]: https://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [サイトの URL]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [証明書をサービスに関連付ける方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg465718.aspx
  [Azure Web ロールで Node.js アプリケーションの SSL を構成する]: /ja-jp/develop/nodejs/common-tasks/enable-ssl/
  [HTTPS エンドポイント上での SSL 証明書の構成方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff795779.aspx
