<properties 
	pageTitle="Web アプリケーションで Azure CDN からコンテンツを配信する" 
	description="CDN からのコンテンツを使用して、Web アプリケーションのパフォーマンスを高める方法を説明するチュートリアル。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="cephalin"/>

# Web アプリケーションで Azure CDN からコンテンツを配信する #

このチュートリアルでは、Azure CDN の利点を活用して Web アプリケーションの適用範囲を拡大し、パフォーマンスを向上させる方法について説明します。Azure CDN を使用すると、以下の場合に Web アプリケーションのパフォーマンスが向上します。

- ページに静的または半静的コンテンツへの多数のリンクがある
- アプリケーションがクライアントによってグローバルにアクセスされる
- Web サーバーのトラフィックをオフロードする
- Web サーバーへの同時クライアント接続の数を削減する (詳細については「[Bundling and Minification (バンドルと縮小)](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)」を参照してください) 
- 認識されるページの読み込み/更新時間を削減する

## 学習内容 ##

このチュートリアルで学習する内容は次のとおりです。

-	[Azure CDN エンドポイントから静的コンテンツを配信する](#deploy)
-	[ASP.NET アプリケーションから CDN エンドポイントへのコンテンツのアップロードを自動化する](#upload)
-	[必要なコンテンツの更新が反映されるように CDN キャッシュを構成する](#update)
-	[クエリ文字列を使用して即座に最新コンテンツを配信する](#query)

## 前提条件 ##

このチュートリアルの前提条件は次のとおりです。

-	アクティブな [Microsoft Azure アカウント](/account/)。試用アカウントにサインアップできます。
-	Visual Studio 2013 と BLOB 管理 GUI 用 [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (「[ASP.NET アプリケーションから CDN エンドポイントへのコンテンツのアップロードを自動化する](#upload)」で使用)

> [AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。+ [無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)ことができます。- Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。+ [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/)こともできます。- MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

<a name="static"></a>
## Azure CDN エンドポイントから静的コンテンツを配信する ##

このチュートリアル セクションでは、CDN を作成および使用して静的コンテンツを配信する方法について説明します。主な手順は次のとおりです。

1. ストレージ アカウントの作成
2. ストレージ アカウントにリンクされた CDN の作成
3. ストレージ アカウントでの BLOB コンテナーの作成
4. BLOB コンテナーへのコンテンツのアップロード
5. CDN URL を使用してアップロードしたコンテンツへのリンク

さあ、始めましょう。次の手順に従って、Azure CDN の使用を開始します。

1. CDN エンドポイントを作成するには、[Azure 管理ポータル](http://manage.windowsazure.com/)にログインします。 
1. **[新規]、[データ サービス]、[ストレージ]、[簡易作成]** の順にクリックしてストレージ アカウントを作成します。URL、場所を指定して、**[ストレージ アカウントの作成]** をクリックします。 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE]後で CDN をテストするときに北米から十分な距離の場所が必要であるため、リージョンに東アジアを使用していることに注意してください。

2. 新しいストレージ アカウントのステータスが **[オンライン]** になった後で、作成したストレージ アカウントに関連付けられた新しい CDN エンドポイントを作成します。**[新規]、[アプリケーション サービス]、[CDN]、[簡易作成]** の順にクリックします。作成したストレージ アカウントを選択して **[作成]** をクリックします。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE]CDN が作成されると、その URL および関連付けられた元のドメインが Azure ポータルに表示されます。ただし、CDN エンドポイントの構成がすべてのノードの場所に完全に反映されるまで少し時間がかかる場合があります。

3. ping を使用して CDN エンドポイントをテストし、オンラインであることを確認します。CDN エンドポイントがすべてのノードへの反映を完了していない場合は、次のようなメッセージが表示されます。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	1 時間後にテストをもう一度実行してください。CDN エンドポイントがノードへの反映を完了すると、ping への応答が返されます。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. この時点で、CDN エンドポイントが最も近い CDN ノードを既に決定したことを確認できます。デスクトップ コンピューターで確認した応答 IP アドレスが **93.184.215.201** であるとします。これを [www.ip-address.org](http://www.ip-address.org) のようなサイトで調べると、サーバーが Washington D.C. にあることがわかります。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	すべての CDN ノードの場所の一覧については、[Azure Content Delivery Network (CDN) ノードの場所](http://msdn.microsoft.com/library/azure/gg680302.aspx)に関するページを参照してください。

3. Azure ポータルに戻り、**[CDN]** タブで、作成した CDN エンドポイントの名前をクリックします。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. **[クエリ文字列の有効化]** をクリックし、Azure CDN キャッシュ内のクエリ文字列を有効にします。これを有効にすると、同一のリンクが異なるクエリ文字列でアクセスされた場合は別のエントリとしてキャッシュされます。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE]チュートリアルのこの部分でのクエリ文字列の有効化は必須ではありませんが、ここでの変更が残りのノードに反映されるまで時間がかかるため、できる限り早めに有効にしておくと便利です。また、クエリ文字列非対応コンテンツで CDN キャッシュが停滞するのを防ぐためでもあります (CDN コンテンツの更新については後で説明します)。これを活用する方法については、「[クエリ文字列を使用して即座に最新コンテンツを配信する](#query)」を参照してください。

6. Visual Studio 2013 のサーバー エクスプローラーで、[**Microsoft Azure への接続**] をクリックします。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  プロンプトに従って Azure アカウントにサインインします。
8.  サインインした後で、**[Microsoft Azure]、[ストレージ] の順に展開し、自分のアカウントを展開**します。**[BLOB]** を右クリックし、**[BLOB コンテナーの作成]** をクリックします。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	BLOB コンテナー名を指定して **[OK]** をクリックします。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	サーバー エクスプローラーで、作成した BLOB コンテナーを管理するためにダブルクリックします。中央のウィンドウに管理インターフェイスが表示されます。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	**[BLOB のアップロード]** をクリックし、Web ページで使用される画像、スクリプト、またはスタイルシートを BLOB コンテナーにアップロードします。アップロードの進捗状況は [**Azure のアクティビティ ログ**] に表示され、アップロードされた BLOB はコンテナー ビューに表示されます。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	これで BLOB のアップロードが完了しました。次に、BLOB を公開してアクセスできるようにする必要があります。サーバー エクスプローラーで、コンテナーを右クリックし、**[プロパティ]** を選択します。**[パブリック読み取りアクセス]** プロパティを **[BLOB]** に設定します。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	ブラウザーで 1 つの BLOB の URL に移動して BLOB のパブリック アクセスをテストします。たとえば、`http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` でアップロードしたリスト内の最初の画像をテストできます。

	Visual Studio の BLOB 管理インターフェイスで指定された HTTPS アドレスを使用していない点に注意してください。Azure CDN ではコンテンツへのパブリックなアクセスが必須ですが、HTTP を使用してそれをテストできます。

13.	ブラウザーで BLOB が適切にレンダリングされることを確認できたら、この URL を `http://<yourStorageAccountName>.blob.core.windows.net` から自分の Azure CDN の URL に変更します。この例では、CDN エンドポイントで最初の画像をテストするために `http://az623979.vo.msecnd.net/cdn/cephas_lin.png` を使用します。

	>[AZURE.NOTE]CDN エンドポイントの URL は、Azure 管理ポータルの [CDN] タブで見つけることができます。

	BLOB への直接的なアクセスと CDN アクセスのパフォーマンスを比較すると、Azure CDN の使用によりパフォーマンスが向上することが確認できます。画像の BLOB URL アクセスに関する Internet Explorer 11 F12 開発者ツールのスクリーンショットを次に示します。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	同じ画像の CDN URL アクセスを次に示します。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	**[要求]** の時間の数値、つまり最初のバイトまでの時間、または要求を送信して最初の応答をサーバーから受信するまでにかかる時間に注意してください。東アジア リージョンでホストされている BLOB にアクセスすると、要求は太平洋全体を横断してサーバーに到達するため、266 ミリ秒かかります。ただし、Azure CDN にアクセスするときにかかる時間はわずか 16 ミリ秒です。**パフォーマンスの向上が約 20 倍**にもなります。
	
15.	単に、Web ページ内の新しいリンクを使用するかどうかの問題です。たとえば、次の画像タグを追加できます。

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

このセクションでは、CDN エンドポイントを作成し、コンテンツをアップロードして、任意の Web ページから CDN コンテンツにリンクする方法について学習しました。

<a name="upload"></a>
## ASP.NET アプリケーションから CDN エンドポイントへのコンテンツのアップロードを自動化する ##

ASP.NET Web アプリケーション内にあるすべての静的コンテンツを簡単に CDN エンドポイントにアップロードする場合や、継続的な配信を使用して Web アプリケーションをデプロイする場合 (例については、「[Azure でのクラウド サービスの継続的な配信](../cloud-services/cloud-services-dotnet-continuous-delivery.md)」を参照) には、Azure PowerShell を使用して、Web アプリケーションをデプロイするたびに最新コンテンツ ファイルと Azure BLOB が自動的に同期されるように設定できます。たとえば、「[Upload Content Files from ASP.NET Application to Azure Blobs (ASP.NET アプリケーションから Azure BLOB へのコンテンツ ファイルのアップロード)](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)」でスクリプトを実行し、ASP.NET アプリケーション内のすべてのコンテンツ ファイルをアップロードすることができます。スクリプトを使用するには、次の手順に従います。

4. [**スタート**] メニューの [**Microsoft Azure PowerShell**] を実行します。
5. [Azure PowerShell] ウィンドウで、`Get-AzurePublishSettingsFile` を実行し、Azure アカウントの発行設定ファイルをダウンロードします。
6. 発行設定ファイルをダウンロードしたら、以下を実行します。 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE]発行設定ファイルをインポートすると、これがすべての Azure PowerShell セッションに対して既定の Azure アカウントになります。つまり、この手順は一度だけ実行する必要があります。
	
1. スクリプトを[ダウンロード ページ](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)からダウンロードします。それを ASP.NET アプリケーションのプロジェクト フォルダーに保存します。
2. ダウンロードしたスクリプトを右クリックし、**[プロパティ]** をクリックします。
3. **[ブロックの解除]** をクリックします。
4. PowerShell ウィンドウを開き、以下を実行します。

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

このスクリプトは、*\\Content* フォルダーおよび *\\Scripts* フォルダーのすべてのファイルを、指定されたストレージ アカウントおよびコンテナーにアップロードします。これには次の利点があります。

-	Visual Studio プロジェクトのファイル構造が自動的にレプリケートされる
-	必要に応じて BLOB コンテナーが自動的に作成される
-	個別の BLOB コンテナーに含まれている複数の Web アプリケーションに対して、同じ Azure ストレージ アカウントおよび CDN エンドポイントが再利用される
-	Azure CDN が新しいコンテンツで簡単に更新される。コンテンツの更新の詳細については、「[必要なコンテンツの更新が反映されるように CDN キャッシュを構成する](#update)」を参照してください。

`-StorageContainer` パラメーターには、Web アプリケーションの名前または Visual Studio プロジェクト名を使用すると便利です。汎用 "cdn" をコンテナー名として使用する代わりに、Web アプリケーションの名前を使用することにより、関連するコンテンツを識別しやすい同じコンテナーに編成できます。

コンテンツでアップロードが完了した後で、.cshtml ファイルなどの HTML コード内で `http://<yourCDNName>.vo.msecnd.net/<containerName>` を使用して、*\\Content* フォルダーおよび *\\Scripts* フォルダーにリンクできます。Razor ビューで使用できる例を次に示します。

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

PowerShell スクリプトを継続的な配信構成に統合する例については、「[Azure でのクラウド サービスの継続的な配信](../cloud-services/cloud-services-dotnet-continuous-delivery.md)」を参照してください。

<a name="update"></a>
## 必要なコンテンツの更新が反映されるように CDN キャッシュを構成する ##

BLOB コンテナーの Web アプリケーションから静的ファイルをアップロードした後で、プロジェクトのファイルの 1 つに変更を加え、BLOB コンテナーに再度アップロードします。CDN エンドポイントに自動的に更新されることになっていますが、実際は、コンテンツの CDN URL にアクセスしたときに更新が未反映である場合があります。

実は、CDN は BLOB ストレージから自動的に更新されますが、その際に既定の 7 日間のキャッシング ルールがコンテンツに適用されます。つまり、CDN ノードが BLOB ストレージからコンテンツを取得した後、キャッシュ内で同じコンテンツが期限切れになるまで更新は実行されません。

さいわい、キャッシュの有効期限はカスタマイズできます。ほとんどのブラウザー同様、Azure CDN はコンテンツのキャッシュ制御ヘッダーで指定された有効期限を優先します。カスタムのキャッシュ制御ヘッダーの値を指定するには、Azure ポータルの BLOB コンテナーに移動して BLOB プロパティを編集します。次のスクリーンショットでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

また、PowerShell スクリプトですべての BLOB のキャッシュ制御ヘッダーを設定する方法もあります。「[ASP.NET アプリケーションから CDN エンドポイントへのコンテンツのアップロードを自動化する](#upload)」のスクリプトでは、次のコード スニペットに対して、

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

次のように変更してください。

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

ただし、新しいキャッシュ制御ヘッダーを使用しても、Azure CDN でキャッシュされたコンテンツの有効期限 7 日間が経過しないと新しいコンテンツを取得できない場合があります。これは、JavaScript や CSS の更新のようにコンテンツの更新を即座に有効にしようとする場合には、カスタムのキャッシング値が役に立たないことを意味します。この問題を回避するには、クエリ文字列でファイルの名前を変更するか、バーションを管理します。詳細については、「[クエリ文字列を使用して即座に最新コンテンツを配信する](#query)」を参照してください。

もちろん、キャッシュのための時間と場所があります。コンテンツによっては、頻繁に更新する必要がなくても (たとえば、今度のワールド カップの試合であれば 3 時間ごとに更新すれば問題ありません)、十分なグローバル トラフィックが取得され、自分自身の Web サーバーからオフロードしたい場合があります。これは、Azure CDN キャッシュの使用に適した候補です。

<a name="query"></a>
## クエリ文字列を使用して即座に最新コンテンツを配信する ##

Azure CDN で、クエリ文字列を有効にすることにより、固有のクエリ文字列を含む URL からのコンテンツを別個にキャッシュできます。キャッシュされた CDN コンテンツの期限切れまで待機せずに、特定のコンテンツの更新をクライアント ブラウザーに即座にプッシュする場合に、この機能は役立ちます。URL にバージョン番号を含む Web ページを発行するとします。
  
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

CSS 更新を発行して、CSS URL で別のバージョン番号を使用する場合は、次のようになります。

	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

クエリ文字列が有効になっている CDN エンドポイントに対して、2 つの URL は相互に一意であり、新しい *bootstrap.css* を取得するための新しい要求が Web サーバーに対して行われます。ただし、クエリ文字列が有効になっていない CDN エンドポイントに対しては、これらは同じ URL であり、単純にキャッシュされた *bootstrap.css* が提供されます。

対処する方法は、バージョン番号を自動的に更新することです。Visual Studio では、簡単に行うことができます。.cshtml ファイルで前述のリンクを使用し、アセンブリ番号に基づいてバージョン番号を指定できます。

	@{
	    var cdnVersion = System.Reflection.Assembly.GetAssembly(
	        typeof(MyMvcApp.Controllers.HomeController))
	        .GetName().Version.ToString();
	}
	
	...
	
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

すべての発行サイクルの一部としてアセンブリ番号を変更すれば、Web アプリケーションを発行するたびに同様に一意のバージョン番号を取得することができます。これは、次の発行サイクルまでそのまま維持されます。または、Visual Studio プロジェクトで *Properties\\AssemblyInfo.cs* を開き、`*` を `AssemblyVersion` で使用することにより、Web アプリケーションのビルドのたびに Visual Studio でアセンブリ バージョン番号が自動的にインクリメントされます。次に例を示します。

	[assembly: AssemblyVersion("1.0.0.*")]

## ASP.NET にバンドルされているスクリプトおよびスタイルシートについて ##

[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) および [Azure Cloud Services](/services/cloud-services/) では、[ASP.NET のバンドルおよび縮小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)を Azure CDN へ最適に統合できます。

Azure CDN と Azure App Service または Azure Cloud Services の統合には、次の利点があります。

- コンテンツのデプロイメント (イメージ、スクリプト、およびスタイルシート) を、Azure Web アプリの[継続的なデプロイメント](../web-sites-publish-source-control.md) プロセスの一部として統合
- JQuery や Bootstrap のバージョンなど、CDN によって配信される NuGet パッケージを簡単にアップグレード 
- Web アプリケーションと CDN によって配信されるコンテンツを同じ Visual Studio インターフェイスから管理

関連するチュートリアルは、次の項目を参照してください。 - [Azure App Service で Azure CDN を使用する](../cdn-websites-with-cdn.md) - [クラウド サービスと Azure CDN との統合](cdn-cloud-service-with-cdn.md)

Azure App Service Web Apps または Azure Cloud Services と統合しなくても、Azure CDN をスクリプト バンドルに使用することはできますが、次の点に注意してください。

- バンドルされたスクリプトを BLOB ストレージに手動でアップロードする必要があります。プログラムによるソリューションは、[stackoverflow](http://stackoverflow.com/a/13736433) で提案されています。
- .cshtml ファイルで、レンダリングされたスクリプト/CSS タグに変更を加え、Azure CDN を使用するようにします。次に例を示します。

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## 詳細情報 ##
- [Azure Content Delivery Network (CDN) の概要](cdn-overview.md)
- [Azure App Service で Azure CDN を使用する](../cdn-websites-with-cdn.md)
- [クラウド サービスと Azure CDN との統合](cdn-cloud-service-with-cdn.md)
- [カスタム ドメインにコンテンツ配信ネットワーク (CDN) コンテンツをマップする方法](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Azure 用 CDN の使用](cdn-how-to-use-cdn.md)
 

<!---HONumber=Oct15_HO3-->