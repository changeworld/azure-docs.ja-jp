<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure Websites で ASP.NET Web フォームの出力キャッシュを使用する方法

ここでは、Azure のキャッシュ サービス (プレビュー) を使用して、ASP.NET Web フォームの ASP.NET ページ出力キャッシュをサポートする方法を説明します。ページ出力キャッシュは、指定した期間だけキャッシュ内のレンダリング ページを直接返す機能です。この機能は、更新頻度の低いページに頻繁にアクセスする場合に役立ちます。ASP.NET MVC アプリケーションはページ出力キャッシュをサポートしていないことに注意してください。

Cache Service (プレビュー) は、Web サイトの外部に分散キャッシュ サービスを提供します。これにより、Web サイトのすべてのインスタンスがキャッシュ内の同じレンダリング ページにアクセスできます。

キャッシュ サービス (プレビュー) を使用してページ出力キャッシュにアクセスする基本手順は次のとおりです。

-   [キャッシュを作成します。][キャッシュを作成します。]
-   [Azure のキャッシュを使用するように ASP.NET プロジェクトを構成します。][Azure のキャッシュを使用するように ASP.NET プロジェクトを構成します。]
-   [web.config ファイルを修正します。][web.config ファイルを修正します。]
-   [出力キャッシュを使用して、キャッシュされたページを一時的に返します。][出力キャッシュを使用して、キャッシュされたページを一時的に返します。]

## <span id="createcache"></span></a>キャッシュを作成する

Managed Cache Service のキャッシュ インスタンスは、PowerShell コマンドレットを使用して作成します。

> PowerShell コマンドレットを使用して Managed Cache Service インスタンスを作成すると、そのインスタンスは [Azure 管理ポータル][Azure 管理ポータル]で表示および構成できるようになります。

Managed Cache Service インスタンスを作成するには、Azure PowerShell コマンド ウィンドウを開きます。

> Azure PowerShell のインストール手順と使用方法については、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

[Add-AzureAccount][Add-AzureAccount] コマンドレットを呼び出し、使用しているアカウントに関連付けられた電子メール アドレスとパスワードを入力します。[Add-AzureAccount][Add-AzureAccount] コマンドレットを呼び出すと、サブスクリプションが既定で選択されて表示されます。サブスクリプションを変更するには、[Select-AzureSubscription][Select-AzureSubscription] コマンドレットを呼び出します。

> アカウントの証明書を使用して Azure PowerShell を構成した場合は、このステップを省略できます。Azure PowerShell と Azure アカウントの接続の詳細については、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

サブスクリプションが既定で選択されて表示されます。サブスクリプションを変更するには、[Select-AzureSubscription][Select-AzureSubscription] コマンドレットを呼び出します。

[New-AzureManagedCache][New-AzureManagedCache] コマンドレットを呼び出して、キャッシュの名前、リージョン、キャッシュ オファリング、およびサイズを指定します。

**[名前]** に、キャッシュ エンドポイントに使用するサブドメイン名を入力します。エンドポイントは、数字と小文字のみを含む、先頭が文字の 6 ～ 12 文字の文字列にしてください。

**[場所]** としてキャッシュのリージョンを指定します。パフォーマンスを最高にするには、キャッシュ クライアント アプリケーションと同じリージョンにキャッシュを作成します。

**Sku** と**メモリ**の組み合わせにより、キャッシュのサイズが決定されます。Managed Cache Service は、次の 3 つのレベルで利用できます。

-   基本 - キャッシュ サイズ 128 MB ～ 1 GB (128 MB ずつ増分)、既定で名前付きキャッシュ 1 つ
-   標準 - キャッシュ サイズ 1 GB ～ 10 GB (1 GB ずつ増分)、通知と最大 10 の名前付きキャッシュのサポート
-   プレミアム - キャッシュ サイズ 5 GB ～ 150 GB (5 GB ずつ増分)、通知、高可用性、および最大 10 の名前付きキャッシュのサポート

アプリケーションのニーズを満たす **Sku** と**メモリ**を選択します。一部のキャッシュ機能 (通知や高可用性など) は、特定のキャッシュ オファリングでのみ使用できる点に注意してください。アプリケーションに最適なキャッシュ オファリングとサイズを選択する方法の詳細については、[キャッシュ オファリング][キャッシュ オファリング] を参照してください。

次の例では、128 MB の基本キャッシュが contosocache という名前で、South Central US リージョンに作成されます。

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> キャッシュを作成する際に使用できるすべてのパラメーターと値の一覧については、[New-AzureManagedCache][New-AzureManagedCache] コマンドレットのドキュメントを参照してください。

PowerShell コマンドレットを呼び出した後、キャッシュが作成されるまで数分かかる場合があります。キャッシュが作成されると、その新しいキャッシュは `Running` 状態になり、既定の設定で使用することができ、[Azure 管理ポータル][Azure 管理ポータル]で表示および構成することができます。

Azure PowerShell ウィンドウで作成の進捗状況をモニタリングできます。キャッシュが使用可能になると、次の例に示すように [New-AzureManagedCache][New-AzureManagedCache] コマンドレットでキャッシュ情報が表示されます。

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>

この後のセクションでは、**[ダッシュボード]** タブを使用して ASP.NET プロジェクトのキャッシュを構成します。

## <span id="configureproject"></span></a>ASP.NET プロジェクトを構成する

1.  まず、**Azure SDK for .NET** の[最新バージョンをインストール][最新バージョンをインストール]済みであることを確認します。

2.  Visual Studio の**ソリューション エクスプローラー**で目的の ASP.NET プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します (WebMatrix を使用している場合は、ツール バーの **[NuGet]** をクリックします)。

3.  **[オンライン検索]** ボックスに「**WindowsAzure.Caching**」と入力します。

    ![NuGet ダイアログ][NuGet ダイアログ]

4.  **[Azure Caching]** パッケージを選択し、**[インストール]** をクリックします。

## <span id="configurewebconfig"></span></a>Web.Config ファイルを修正する

NuGet パッケージは、キャッシュに必要なアセンブリ参照を作成するほか、web.config ファイルにスタブ エントリを追加します。キャッシュ サービスを使用して ASP.NET ページ出力キャッシュにアクセスするには、web.config を一部修正する必要があります。

1.  ASP.NET プロジェクトの **web.config** ファイルを開きます。

2.  **caching** 要素と **outputCache** 要素が既に存在する場合は、それらをコメント アウト (または削除) します。

3.  Azure Caching NuGet パッケージによって追加された **caching** 要素をコメント解除します。次のスクリーン ショットのようになります。

    ![OutputConfig][OutputConfig]

4.  次に、**dataCacheClients** セクションを探します。**securityProperties** 子要素をコメント解除します。

    ![CacheConfig][CacheConfig]

5.  **autoDiscover** 要素で、**identifier** 属性にキャッシュのエンドポイント URL を設定します。エンドポイント URL を確認するには、Azure の管理ポータルでキャッシュのプロパティを参照します。**[ダッシュボード]** タブを開き、**[概要]** セクションの **[エンドポイント URL]** の値をコピーします。

    ![エンドポイント URL][エンドポイント URL]

6.  **messageSecurity** 要素で、**authorizationInfo** 属性にキャッシュのアクセス キーを設定します。アクセス キーを確認するには、Azure の管理ポータルで該当するキャッシュを選択します。下部のバーにある **[キーの管理]** アイコンをクリックします。**[プライマリ アクセス キー]** ボックスの横にあるコピー ボタンをクリックします。

    ![キーの管理][キーの管理]

## <span id="useoutputcaching"></span></a>出力キャッシュを使用する

最後に、出力キャッシュを使用する ASP.NET Web フォーム アプリケーションでページを構成します。そのためには、.ASPX ソースの先頭に **OutputCache** 属性を追加します。次に例を示します。

    <%@ OutputCache Duration="45" VaryByParam="*" %>

上の例では、ページを 45 秒間キャッシュに格納します。**VaryByParam** が "\*" に設定されているので、別のクエリ パラメーターが渡された場合でも、キャッシュ内のこのページ出力は変更されません。一方、次の例では、"UserId" パラメーターの値ごとに別のバージョンのページがキャッシュされます。

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>   

  [キャッシュを作成します。]: #createcache
  [Azure のキャッシュを使用するように ASP.NET プロジェクトを構成します。]: #configureproject
  [web.config ファイルを修正します。]: #configurewebconfig
  [出力キャッシュを使用して、キャッシュされたページを一時的に返します。]: #useoutputcaching
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [Azure PowerShell のインストールおよび構成方法]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/ja-jp/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/ja-jp/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [最新バージョンをインストール]: http://www.windowsazure.com/ja-jp/downloads/?sdk=net
  [NuGet ダイアログ]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
  [OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
  [CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
  [エンドポイント URL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
  [キーの管理]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
