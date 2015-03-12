<properties 
	pageTitle="クラウド サービス (Node.js) 向けの SSL の構成 - Azure" 
	description="Node.js Web ロールの HTTPS エンドポイントを指定する方法、および SSL 証明書をアップロードしてアプリケーションを保護する方法について説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>




# Azure Web ロールで Node.js アプリケーションの SSL を構成する

Secure Socket Layer (SSL) の暗号化は、インターネットを介して送信されるデータを
セキュリティで保護する際に最もよく使用される方法です。この一般的なタスクでは、
Web ロールで Azure Cloud Services としてホストされる Node.js アプリケーションの HTTPS エンドポイントを指定する方法と、
SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。

> [AZURE.NOTE] この記事で説明する手順は、Web ロールで Azure Cloud Services としてホストされる node アプリケーションにのみ適用されます。Websites については、「[Azure Websites の SSL 証明書の構成](../web-sites-configure-ssl-certificate/)」を参照してください。

このタスクの手順は次のとおりです。

-   [手順 1:Node.js サービスを作成してクラウドにサービスを発行する]
-   [手順 2:SSL 証明書を取得する]
-   [手順 3.SSL 証明書をインポートする]
-   [手順 4:サービス定義ファイルとサービス構成ファイルを変更する]
-   [手順 5.HTTPS を使用してロール インスタンスに接続する]

## <a name="step1"> </a>手順 1:Node.js サービスを作成してクラウドにサービスを発行する

Node.js アプリケーションを Azure Web ロールにデプロイすると、
サーバー証明書と SSL 接続は インターネット インフォメーション サービス (IIS) によって管理されるため、
Node.js サービスを http サービスと同様に
記述できます。Azure PowerShell を使用して、以下の手順に従って簡単な Node.js 'hello
world' サービスを作成できます。

1. **[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

	![Azure PowerShell icon][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  **New-AzureServiceProject** コマンドレットを使用して、新しいサービス プロジェクトを作成します。 

	![][1]

3.  **Add-AzureNodeWebRole** コマンドレットを使用して、サービスに Web ロールを追加します。

    ![][2]

4.  **Publish-AzureServiceProject** コマンドレットを使用して、サービスをクラウドに発行します。

    ![][3]

	> [AZURE.NOTE] 以前に Azure サブスクリプションの発行設定をインポートしていない場合、発行しようとするとエラーが出力されます。サブスクリプションの発行設定のダウンロードとインポートについては、[Azure PowerShell for Node.js の使用方法](https://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)に関するページを参照してください。

**Publish-AzureServiceProject** コマンドレットから返される**作成された Web サイトの URL** の値には、ホステッド アプリケーションの完全修飾ドメイン名が含まれます。この特定の完全修飾ドメイン名について SSL 証明書を取得し、Azure に展開する必要があります。

## <a name="step2"> </a>手順 2:SSL 証明書を取得する

アプリケーションの SSL を構成するには、最初に、
セキュリティ保護のための証明書を発行する信頼されたサード パーティである、
証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。まだ
SSL 証明書がない場合は、SSL 証明書を販売する会社から
取得する必要があります。

証明書は、Azure における SSL 証明書の次の要件を
満たす必要があります。

-   証明書は秘密キーを含む必要があります。
-   証明書はキー交換のために作成される必要があります (.pfx ファイル)。
-   証明書のサブジェクト名は、クラウド サービスへのアクセスに使用されるドメインと
    一致する必要があります。cloudapp.net ドメインの SSL 証明書を取得することはできないため、
    証明書のサブジェクト名はアプリケーションへのアクセスに使用されるカスタム ドメイン名と
    一致する必要があります。たとえば、「__mysecuresite.cloudapp.net__」のように入力します。
-   証明書では、2048 ビット以上の暗号化を使用する必要があります。

## <a name="step3"> </a>手順 3:SSL 証明書をインポートする

証明書を取得したら、開発用コンピューターの証明書ストアに証明書をインストールします。この証明書は、後の手順で行う構成の変更に基づくアプリケーションの展開パッケージの一部として取得され、Azure にアップロードされます。

> [AZURE.NOTE] このセクションの手順は、Windows 8 バージョンの証明書のインポート ウィザードに基づいています。以前のバージョンの Windows を使用している場合、ここで示されている手順はウィザードに表示される順序と一致しないことがあります。そのような場合は、証明書のインポート ウィザードを使用する前にこのセクション全体を通読して、実行する必要がある全体的なアクションを理解しておいてください。

SSL 証明書をインポートするには、次の手順を実行します。

1.   エクスプローラーを使用して、証明書が含まれている **.pfx** ファイルがあるディレクトリに移動し、証明書をダブルクリックします。証明書のインポート ウィザードが表示されます。
	
	![certificate wizard][cert-wizard]

2.   **[ストアの場所]** で、**[現在のユーザー]** を選択し、**[次へ]** をクリックします。使用しているユーザー アカウントの証明書ストアに証明書がインストールされます。

3.   **[秘密キーの保護]** 画面が表示されるまで、既定値を受け入れながらウィザードの手順を続行します。この画面で、証明書のパスワード (ある場合) を入力する必要があります。また、**[このキーをエクスポート可能にする]** も選択する必要があります。最後に、**[次へ]** をクリックします。

	![private key protection][key-protection]

4. 証明書が正常にインストールされるまで、既定値を受け入れながらウィザードの手順を続行します。

次に、インストールした証明書を参照するようにサービス定義を
変更する必要があります。

## <a name="step4"> </a>手順 4:サービス定義ファイルとサービス構成ファイルを変更する

アプリケーションは、証明書を参照するように構成する必要があります。
また、HTTPS エンドポイントを追加する必要があります。その結果として、サービス定義ファイルおよびサービス構成ファイルを
更新する必要があります。

1.  サービス ディレクトリで、サービス定義ファイル
    (ServiceDefinition.csdef) を開き、**WebRole** セクション内に **Certificates** セクションを追加し、証明書に関する次の情報を含めます
    。

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    **Certificates** セクションでは、証明書の名前、
    場所、およびこの証明書があるストアの名前を定義します。ユーザーの証明書ストアに証明書をインストールしたため、"My" という値が使用されます。他の証明書ストアの場所も使用できます。詳細については、「[サービスと証明書の関連付け]」を参照してください。

2.  サービス定義ファイルで、**Endpoints** セクション内の http **InputEndpoint** 要素を更新し、HTTPS を有効にします。

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    サービス定義ファイルに対して必要な変更はすべて完了しましたが、
    サービス構成ファイルに証明書の情報を追加する必要もあります
    。

3.  サービス構成ファイル
    (**ServiceConfiguration.Cloud.cscfg** と
    **ServiceConfiguration.Local.cscfg**) で、
    **Role** セクション内の空の **Certificates** セクションに証明書を追加し、
    次に示すサムプリント値のサンプルを証明書のサムプリント値に置き換えます
    。

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  もう一度サービスを発行して、クラウド内の構成を最新の情報に
    更新します。Azure PowerShell プロンプトで、
    サービス ディレクトリから「**Publish-AzureServiceProject**」と入力します。

	発行プロセスの中で、参照された証明書がローカル証明書ストアからコピーされ、デプロイ パッケージに含められます。

## <a name="step5"> </a>手順 5:HTTPS を使用してロール インスタンスに接続する

Azure でデプロイを実行できるようになったため、HTTPS を使用して
接続できます。

1.  管理ポータルで、目的のクラウド サービスを選択し、**[ダッシュボード]** をクリックします。

2. 下へスクロールし、**[サイトの URL]** として表示されているリンクをクリックします。

    ![the site url][site-url]

	> [AZURE.IMPORTANT] ポータルに表示されるサイトの URL で HTTPS が指定されていない場合は、ブラウザーで HTTP の代わりに HTTPS を使用して手動で URL を入力する必要があります。

3.  新しいブラウザーが開いて、Web サイトが表示されます。

    ブラウザーには、HTTPS 接続を使用していることを示す
    錠前のアイコンが表示されます。これは、アプリケーションの SSL が
    正しく構成されていることも表します。

    ![][8]

## その他のリソース

[証明書をサービスに関連付ける方法]

[Azure ワーカー ロールで Node.js アプリケーションの SSL を構成する]

[HTTPS エンドポイントでの SSL 証明書の構成方法]

  [手順 1.Node.js サービスを作成してクラウドにサービスを発行する]: #step1
  [手順 2:SSL 証明書を取得する]: #step2
  [手順 3.SSL 証明書をインポートする]: #step3
  [手順 4:サービス定義ファイルとサービス構成ファイルを変更する]: #step4
  [手順 5.HTTPS を使用してロール インスタンスに接続する]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  [証明書をサービスに関連付ける方法]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [HTTPS エンドポイントでの SSL 証明書の構成方法]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Azure ワーカー ロールで Node.js アプリケーションの SSL を構成する]: /ja-jp/develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=45--> 
