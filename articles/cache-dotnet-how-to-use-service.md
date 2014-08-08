<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Azure のキャッシュ サービス (プレビュー) の使用方法" authors=""  solutions="" writer="" manager="" editor=""  />



#Azure のキャッシュ サービス (プレビュー) の使用方法

このガイドでは、**Azure のキャッシュ サービス 
(プレビュー)**の基本的な使用方法について説明します。サンプルは C\# コードで記述され、
.NET API を利用しています。紹介するシナリオは、**キャッシュの作成と構成**、**キャッシュ クライアントの構成**、**キャッシュでのオブジェクトの追加と削除、キャッシュへの ASP.NET セッション状態の格納**、**キャッシュの使用による ASP.NET ページ出力キャッシュの有効化**などです。Azure の
キャッシュの使い方の詳細については、「[次のステップ][]」を参照してください。

## 目次

-   [Azure のキャッシュとは][]
-	[キャッシュ サービス (プレビュー) を使い始める方法]
	-	[キャッシュの作成][]
	-	[キャッシュの構成][]
	-	[キャッシュ クライアントの構成][]
-	[キャッシュの操作][]
	-	[方法: DataCache オブジェクトを作成する][]
	-   [方法: キャッシュでオブジェクトを追加および削除する][]
	-   [方法: キャッシュ内のオブジェクトの有効期限を指定する][]
	-   [方法: ASP.NET セッション状態をキャッシュに格納する][]
	-   [方法: ASP.NET ページ出力キャッシュをキャッシュに格納する][]
-   [次のステップ][]

<a name="what-is"></a>
## Azure のキャッシュとは

Azure のキャッシュ サービス (プレビュー) とは、拡張性の高い分散型メモリ内ソリューションです。これを使用すると、データへの超高速アクセスを提供することで、拡張性や応答性の高いアプリケーションを作成できます。

Azure のキャッシュ サービス (プレビュー) には、
次のような機能があります。

-   セッション状態とページ出力キャッシュに対応した、
    構築済みの ASP.NET プロバイダーにより、アプリケーション コードを
    変更しなくても Web アプリケーションの機能を強化できます。
-   任意のシリアル化可能なマネージ オブジェクトをキャッシュできます。たとえば、
    CLR オブジェクト、行、XML、バイナリ データなどです。
-   Azure と Windows Server AppFabric の両方で、
    開発モデルの一貫性を確保できます。

キャッシュ サービス (プレビュー) を使用すると、マイクロソフトによって管理されている、セキュリティで保護された専用キャッシュにアクセスできます。キャッシュ サービス (プレビュー) を使用して作成されたキャッシュには、Azure Web サイト、Web ロールとワーカー ロール、および仮想マシンで実行されている Azure 内のアプリケーションからアクセスできます。

キャッシュ サービス (プレビュー) は、次の 3 つのレベルで利用できます。

-	基本 - 128 MB ～ 1 GB のキャッシュ
-	標準 - 1 GB ～ 10 GB のキャッシュ
-	プレミアム - 5 GB ～ 150 GB のキャッシュ

各レベルは、機能と料金ごとに異なります。機能については、このガイドで後述します。料金の詳細については、「[キャッシュの料金詳細][]」を参照してください。

このガイドでは、キャッシュ サービス (プレビュー) の基本的な概要について説明します。この概要ガイドでは扱われていない機能の詳細については、「[Azure のキャッシュ サービス (プレビュー)][]」を参照してください。

<a name="getting-started-cache-service"></a>
## キャッシュ サービス (プレビュー) を使い始める方法

キャッシュ サービス (プレビュー) は、簡単に使い始めることができます。使い始めるには、キャッシュをプロビジョニングして構成します。次に、キャッシュ クライアントを構成してキャッシュにアクセスできるようにします。キャッシュ クライアントを構成すると、使い始めることができます。

-	[キャッシュの作成][]
-	[キャッシュの構成][]
-	[キャッシュ クライアントの構成][]

<a name="create-cache"></a>
## キャッシュの作成

キャッシュを作成するには、まず[管理ポータル][]にサインインします。

>初めてキャッシュ サービス (プレビュー) を使用する場合、キャッシュ サービス プレビュー プログラムへのアクセスを要求する必要があります。プレビュー プログラムにサインアップするには、**[新規]**、**[データ サービス]**、**[Cache プレビュー]**、**[プレビュー プログラム]** の順にクリックします。表示される手順に従ってキャッシュ サービス プレビュー プログラムへのアクセスを要求し、アクセスが許可されたら、次に手順に進みます。

**[新規]**、**[データ サービス]**、**[Cache プレビュー]**、**[簡易作成]** の順にクリックします。

![NewCacheMenu][NewCacheMenu]

![QuickCreate][QuickCreate]

**Endpoint** に、キャッシュ エンドポイントに使用するサブドメイン名を入力します。エンドポイントは、数字と小文字のみを含む、先頭が文字の 6 ～ 12 文字の文字列にしてください。

**[リージョン]** ボックスで、キャッシュのリージョンを選択します。パフォーマンスを最高にするには、キャッシュ クライアント アプリケーションと同じリージョンにキャッシュを作成します。

**[サブスクリプション]** で、キャッシュに使用する Azure サブスクリプションを選択します。

>アカウントにサブスクリプションが 1 つしかない場合、自動的に選択されるため、[サブスクリプション] ドロップダウン リストは表示されません。

**キャッシュ オファリング**と**キャッシュ メモリ**の組み合わせにより、キャッシュのサイズが決定されます。キャッシュ サービス (プレビュー) は、次の 3 つのレベルで利用できます。

-	基本 - キャッシュ サイズ 128 MB ～ 1 GB (128 MB ずつ増分)、既定で名前付きキャッシュ 1 つ
-	標準 - キャッシュ サイズ 1 GB ～ 10 GB (1 GB ずつ増分)、通知と最大 10 の名前付きキャッシュのサポート

-	プレミアム - キャッシュ サイズ 5 GB ～ 150 GB (5 GB ずつ増分)、通知、高可用性、および最大 10 の名前付きキャッシュのサポート


アプリケーションの必要を満たす**キャッシュ オファリング**と**キャッシュ メモリ**を選択します。一部のキャッシュ機能 (通知や高可用性など) は、特定のキャッシュ オファリングでのみ使用できる点に注意してください。アプリケーションに最適なキャッシュ オファリングとサイズを選択する方法の詳細については、「[Azure のキャッシュ サービス (プレビュー) のキャッシュ オファリング][]」と「[Azure のキャッシュ サービス (プレビュー) の容量計画][]」を参照してください。

新しいキャッシュ オプションが構成されたら、**[新しいキャッシュの作成]** をクリックします。キャッシュが作成されるまで数分かかる場合があります。状態を確認するには、ポータルの下部にある通知を監視します。キャッシュが作成されると、新しいキャッシュの状態が "実行中" になって、既定の設定で使用できるようになります。キャッシュの構成をカスタマイズするには、次の「[キャッシュの構成][]」セクションを参照してください。


<a name="enable-caching"></a>
## キャッシュの構成

管理ポータルにあるキャッシュの **[構成]** タブでは、キャッシュのオプションを構成できます。各キャッシュには**既定の**名前付きキャッシュがあり、標準およびプレミアム キャッシュ オファリングでは最大 9 つの名前付きキャッシュ (合計 10) がサポートされます。名前付きキャッシュにはそれぞれ独自のオプション セットがあり、柔軟性の高い方法でキャッシュを構成できます。

![NamedCaches][NamedCaches]

名前付きキャッシュを作成するには、新しいキャッシュの名前を **[名前]** ボックスに入力して必要なオプションを指定し、**[保存]** をクリックした後、**[はい]** をクリックして確認します。変更を取り消すには、**[破棄]** をクリックします。

## [有効期限ポリシー] と [時間 (分)] ##

**[有効期限ポリシー]** と **[時間 (分)]** 設定の組み合わせにより、キャッシュされた項目の有効期限が決まります。有効期限ポリシーには、**[絶対]**、**[スライド]**、および **[なし]** の 3 つの種類があります。

**[絶対]** が指定されている場合、項目がキャッシュに追加されると、**[時間 (分)]** により指定された有効期間が始まります。**[時間 (分)]** により指定された期間が経過すると、項目は期限切れになります。

**[スライド]** が指定されている場合、キャッシュ内の項目にアクセスがあるたびに、**[時間 (分)]** により指定された有効期間がリセットされます。項目への最後のアクセスから、**[時間 (分)]** により指定された期間が経過するまで、項目は期限切れになりません。

**[なし]** が指定されている場合、**[時間 (分)]** は **0** に設定する必要があります。項目は期限切れになりません。

既定の有効期限ポリシーは **[絶対]** で、**[時間 (分)]** の既定の設定は 10 分です。有効期限ポリシーは、名前付きキャッシュ内の項目ごとに固定されますが、**[時間 (分)]** は、タイムアウト パラメーターを受け取る **Add** オーバーロードと **Put** オーバーロードを使用することで項目ごとにカスタマイズできます。

削除ポリシーと有効期限ポリシーの詳細については、「[Azure のキャッシュ サービス (プレビュー) の有効期限と削除][]」を参照してください。

## 通知 ##

キャッシュ クラスターでさまざまなキャッシュ操作が発生したときに、アプリケーションが非同期通知を受け取ることができるようにするキャッシュ通知です。キャッシュ通知は、ローカルにキャッシュされた自動無効化も行います。詳細については、「[Azure のキャッシュ サービス (プレビュー) の通知][]」を参照してください。

>通知は、標準およびプレミアム キャッシュ オファリングでのみ使用できます。基本キャッシュ オファリングでは使用できません。詳細については、「[Azure のキャッシュ サービス (プレビュー) のキャッシュ オファリング][]」を参照してください。

## 高可用性 ##

高可用性が有効な場合、バックアップ コピーはキャッシュに追加された各項目で作成されます。項目のプライマリ コピーで予期しないエラーが発生した場合でも、バックアップ コピーを使用できます。

その名が示すとおり、高可用性を使用すると、各キャッシュ項目に必要なメモリの量が 2 倍に増えます。容量計画を行うときは、このメモリの影響を考慮に入れてください。詳細については、「[Azure のキャッシュ サービス (プレビュー) の高可用性][]」を参照してください。

>高可用性は、プレミアム キャッシュ オファリングでのみ使用できます。基本および標準 キャッシュ オファリングでは使用できません。詳細については、「[Azure のキャッシュ サービス (プレビュー) のキャッシュ オファリング][]」を参照してください。

## 削除 ##

キャッシュ内で使用可能なメモリ容量を維持するため、Least Recently Used (LRU) 削除がサポートされています。メモリ消費量がメモリしきい値を超えると、メモリ容量に余裕が出るまで、期限切れかどうかに関係なくオブジェクトがメモリから削除されます。
削除は、既定で有効です。削除が無効な場合、容量に到達しても項目はキャッシュから削除されないため、Put 操作と Add 操作に失敗します。

削除ポリシーと有効期限ポリシーの詳細については、「[Azure のキャッシュ サービス (プレビュー) の有効期限と削除][]」を参照してください。

キャッシュの構成が済んだら、キャッシュにアクセスできるようにキャッシュ クライアントを構成できます。

<a name="NuGet"></a>
## キャッシュ クライアントの構成

キャッシュ サービス (プレビュー) を使用して作成されたキャッシュには、Azure Web サイト、Web ロールとワーカー ロール、および仮想マシンで実行されている Azure アプリケーションからアクセスできます。キャッシュ クライアント アプリケーションの構成を容易にする NuGet パッケージが用意されています。

Cache NuGet パッケージを使用してクライアント アプリケーションを構成するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

![NuGetPackageMenu][NuGetPackageMenu]

**[Azure の Caching]** を選択し、**[インストール]**、[同意する] の順にクリックします。

>一覧に **[Azure の Caching]** が表示されない場合は、**[オンライン検索]** ボックスに「**WindowsAzure.Caching**」と入力し、結果の中から選択します。

![NuGetPackage][NuGetPackage]

NuGet パッケージは、いくつかの処理を行います。アプリケーションの構成ファイルに必要な構成を追加したり、必要なアセンブリ参照を追加したりします。クラウド サービス プロジェクトの場合、クラウド サービスの ServiceConfiguration.cscfg ファイルにキャッシュ クライアント診断レベル設定も追加します。

>ASP.NET Web プロジェクトでは、Cache NuGet パッケージは 2 つのコメント アウトされたセクションも web.config に追加します。1 つ目のセクションはセッション状態をキャッシュに格納できるようにし、2 つ目のセクションは ASP.NET ページがキャッシュを出力できるようにします。詳細については、「[方法: ASP.NET セッション状態をキャッシュに格納する]」および「[方法: ASP.NET ページ出力キャッシュをキャッシュに格納する][]」を参照してください。

NuGet パッケージは、次の構成要素をアプリケーションの web.config または app.config に追加します。**dataCacheClients** セクションおよび **cacheDiagnostics** セクションは、**configSections** 要素の下に追加されます。**configSections** 要素が存在しない場合は、**configuration** 要素の子として作成されます。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
  
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


これらの新しいセクションには、**configuration** 要素にも追加された **dataCacheClients** 要素への参照が含まれています。

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


構成が追加されたら、新たに追加された構成で次の 2 つの項目を置き換えます。

1. **[Cache role name or Service Endpoint]** を、管理ポータルのダッシュボードに表示されるエンドポイントに置き換えます。

	![Endpoint][Endpoint]

2. securityProperties セクションをコメント解除し、**[Authentication Key]** を認証キーに置き換えます。認証キーは、キャッシュ ダッシュボードから **[キーの管理]** をクリックすることで管理ポータルに表示されます。

	![AccessKeys][AccessKeys]

>これらの設定を適切に構成しないと、クライアントはキャッシュにアクセスできません。

クラウド サービス プロジェクトの場合、NuGet パッケージは、キャッシュ クライアント ロールの ServiceConfiguration.cscfg 内の **ConfigurationSettings** に、**ClientDiagnosticLevel** 設定も追加します。次の例は、ServiceConfiguration.cscfg ファイルの **WebRole1** セクションで、**ClientDiagnosticLevel** は 1 になっています (**ClientDiagnosticLevel** の既定値)。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>クライアント診断レベルは、キャッシュ クライアントのために収集されるキャッシュ診断情報のレベルを構成します。詳細については、「[Azure のキャッシュ サービス (プレビュー) の ClientDiagnosticLevel について][]」を参照してください。

NuGet パッケージは、次のアセンブリへの参照も追加します。

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

プロジェクトが Web プロジェクトである場合は、次のアセンブリ参照も追加されます。

-	Microsoft.Web.DistributedCache.dll.

>これらのアセンブリは、C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[SDK バージョン]\\ref\\Caching\\ フォルダーにあります。

クライアント プロジェクトをキャッシュ用に構成できたら、以降のセクションで説明されている、キャッシュを操作するための技法を使用できます。

<a name="working-with-caches"></a>
## キャッシュの操作

このセクションの手順では、キャッシュに対する一般的なタスクを行う方法について説明します。

-	[方法: DataCache オブジェクトを作成する][]
-   [方法: キャッシュでオブジェクトを追加および削除する][]
-   [方法: キャッシュ内のオブジェクトの有効期限を指定する][]
-   [方法: ASP.NET セッション状態をキャッシュに格納する][]
-   [方法: ASP.NET ページ出力キャッシュをキャッシュに格納する][]

<a name="create-cache-object"></a>
## 方法: DataCache オブジェクトを作成する

プログラムでキャッシュを操作するには、キャッシュへの参照が必要です。Azure のキャッシュを使用するファイルの先頭に、
次の内容を追加します。

    using Microsoft.ApplicationServer.Caching;

>Cache NuGet パッケージをインストールして、必要な参照が追加された後も、Visual Studio が using ステートメント内の型を認識しない場合は、プロジェクトのターゲット プロファイルが .NET Framework 4 以降であることを確認し、**クライアント プロファイル**と指定されていないプロファイルを選択するようにしてください。キャッシュ クライアントを構成する手順については、「[キャッシュ クライアントの構成][]」を参照してください。

**DataCache** オブジェクトを作成する方法は、2 つあります。1 つ目の方法では、目的のキャッシュの名前を渡して、単純に **DataCache** を作成します。

    DataCache cache = new DataCache("default");

**DataCache** がインスタンス化されたら、以降のセクションで説明されているように、それを使ってキャッシュを操作できます。

2 つ目の方法では、既定のコンストラクターを使用して、アプリケーション内に新しい **DataCacheFactory** オブジェクトを作成します。このようにすると、キャッシュ クライアントが構成ファイル内の設定を使うようになります。**DataCache** オブジェクトを返す、新しい **DataCacheFactory** インスタンスの **GetDefaultCache** メソッドを呼び出すか、**GetCache** メソッドを呼び出してキャッシュの名前を渡します。これらのメソッドは、プログラムでキャッシュにアクセスするために使用できる **DataCache** オブジェクトを返します。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## 方法: キャッシュでオブジェクトを追加および削除する

キャッシュに項目を追加するには、**Add** メソッド
または **Put** メソッドを使用できます。**Add** メソッドは、指定されたオブジェクトを、キー パラメーター
の値でキー付けして、キャッシュに追加します。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

キャッシュに同じキーのオブジェクトが既にある場合、**DataCacheException** がスローされ、次のメッセージが表示されます。

> ErrorCode:SubStatus: Cache に既に存在するキーを使用してオブジェクトを
> 作成しようとしています。Cache はオブジェクトの
> 一意のキー値のみを受け入れます。

特定のキーのオブジェクトを取得するには、**Get** メソッドを使用できます。オブジェクトが存在する場合は
それが返され、存在しない場合は null が返されます。

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

**Put** メソッドは、指定されたキーを持つオブジェクトがキャッシュに
存在しなければそれを追加し、存在すればそのオブジェクトを置き換えます。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 方法: キャッシュ内のオブジェクトの有効期限を指定する

既定では、キャッシュ内の項目はキャッシュに置かれてから 10 分で期限切れになります。これは、管理ポータルのキャッシュの [構成] タブにある **[時間 (分)]** 設定で構成できます。

![NamedCaches][NamedCaches]

**有効期限ポリシー**には、**[なし]**、**[絶対]**、および **[スライド]** の 3 つの種類があります。これらは、**[時間 (分)]** を使用して有効期限を決定する方法を構成します。既定の **[有効期限の種類]** は **[絶対]** であり、キャッシュの項目の有効期限のカウントダウン タイマーは、キャッシュに項目が置かれたときに始まります。指定された時間が経過すると、項目は期限切れになります。**[スライド]** が指定されている場合、項目の有効期限のカウントダウンはキャッシュ内の項目がアクセスされるたびにリセットされ、最後のアクセス以降に指定の時間が経過するまでは期限切れになりません。**[なし]** が指定されている場合、**[時間 (分)]** は **0** に設定されている必要があります。項目は期限切れにならずに、キャッシュ内にある限り有効のままになります。

キャッシュ プロパティで構成されているよりも長いまたは短いタイムアウト間隔にする場合は、キャッシュ内の項目を追加または更新するときに、**TimeSpan** パラメーターを取る **Add** および **Put** のオーバーロードを使用して、特定の期間を指定できます。次の
例では、文字列 **value** が **item** でキー付けされ、
30 分のタイムアウトでキャッシュに追加されます。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

キャッシュ内の項目の残りのタイムアウト間隔を確認するには、
**GetCacheItem** メソッドを使用して、キャッシュ内の項目についての
情報 (残りのタイムアウト間隔など) が含まれている **DataCacheItem** 
オブジェクトを取得できます。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 方法: ASP.NET セッション状態をキャッシュに格納する

Azure のキャッシュのセッション状態プロバイダーは、
ASP.NET アプリケーション用のプロセス外ストレージ メカニズムです。このプロバイダー
を使用すると、セッション状態をメモリ内や SQL Server データベース内ではなく、
Azure のキャッシュ内に格納できます。キャッシュ セッション状態
プロバイダーを使用するには、まずキャッシュを構成し、次に「[キャッシュ サービス (プレビュー) を使い始める方法][]」で説明されているように Cache NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。Cache NuGet パッケージをインストールすると、コメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションが Azure のキャッシュ用のセッション状態プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>Cache NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストールのページ][]からインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。

Azure のキャッシュ用のセッション状態プロバイダーを有効にするには、指定されたセクションをコメント解除します。既定のキャッシュは、提供されるスニペットで指定されています。別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

キャッシュ サービス セッション状態プロバイダーの使い方の詳細については、
[Azure のキャッシュのセッション状態プロバイダーに関するページ][]を参照してください。

<a name="store-page"></a>
## 方法: ASP.NET ページ出力キャッシュをキャッシュに格納する

Azure のキャッシュの出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。このデータは、完全な HTTP 応答
専用です (ページ出力キャッシュ)。プロバイダーは、ASP.NET 4 で導入された
新しい出力キャッシュ プロバイダー機能拡張ポイントに接続されます。
出力キャッシュ プロバイダーを使用するには、まずキャッシュ クラスターを構成し、次に、「[キャッシュ サービス (プレビュー) を使い始める方法][]」で説明されているように Cache NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。Caching NuGet パッケージをインストールすると、次のコメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションが Azure の Caching 用の出力キャッシュ プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>Cache NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストールのページ][]からインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。

Azure のキャッシュ用の出力キャッシュ プロバイダーを有効にするには、指定されたセクションをコメント解除します。既定のキャッシュは、提供されるスニペットで指定されています。別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

出力をキャッシュする各ページに、**OutputCache** ディレクティブを追加します。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

この例では、キャッシュされたページ データが 60 秒間キャッシュに保持され、パラメーターの組み合わせごとに異なるバージョンのページがキャッシュされます。使用できるオプションの詳細については、[OutputCache ディレクティブに関するページ][]を参照してください。

Azure のキャッシュの出力キャッシュ プロバイダーの使い方の詳細については、「[Azure のキャッシュ サービス (プレビュー) 用のページ出力キャッシュ プロバイダー][]」を参照してください。

<a name="next-steps"></a>
## 次のステップ

これで、キャッシュ サービス (プレビュー) の基本を学習できました。
さらに複雑なキャッシュ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [キャッシュ サービス (プレビュー)][]
-	キャッシュ サービス (プレビュー) への移行方法を確認する: [Azure のキャッシュ サービス (プレビュー) への移行][]
-   サンプルをチェックする: [Azure のキャッシュ サービス (プレビュー) のサンプル][]

<!-- INTRA-TOPIC LINKS -->
[次のステップ]: #next-steps
[Azure のキャッシュとは]: #what-is
[Azure のキャッシュの作成]: #create-cache
[最適なキャッシュの種類?]: #choosing-cache
[Azure の Caching を使用するための Visual Studio プロジェクトの準備]: #prepare-vs
[Caching を使用するようにアプリケーションを構成する]: #configure-app
[キャッシュ サービス (プレビュー) を使い始める方法]: #getting-started-cache-service
[キャッシュの作成]: #create-cache
[キャッシュの構成]: #enable-caching
[キャッシュ クライアントの構成]: #NuGet
[キャッシュの操作]: #working-with-caches
[方法: DataCache オブジェクトを作成する]: #create-cache-object
[方法: キャッシュでオブジェクトを追加および削除する]: #add-object
[方法: キャッシュ内のオブジェクトの有効期限を指定する]: #specify-expiration
[方法: ASP.NET セッション状態をキャッシュに格納する]: #store-session
[方法: ASP.NET ページ出力キャッシュをキャッシュに格納する]: #store-page
[サポートされている .NET Framework プロファイル]: #prepare-vs-target-net
  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  
   
<!-- LINKS -->
[Azure の管理ポータル]: http://windows.azure.com/
[方法: プログラムによってキャッシュ クライアントを構成する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg618003.aspx
[Azure のキャッシュのセッション状態プロバイダーに関するページ]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric キャッシュ: Caching のセッション状態]: http://www.microsoft.com/ja-jp/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure のキャッシュ サービス (プレビュー) 用のページ出力キャッシュ プロバイダー]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure の共有キャッシュ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg278356.aspx
[チーム ブログ]: http://blogs.msdn.com/b/windowsazure/
[Azure の Caching]: http://www.microsoft.com/ja-jp/showcase/Search.aspx?phrase=azure+caching
[仮想マシンのサイズの構成方法]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure の Caching の容量計画に関する考慮事項]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure の Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[方法: 宣言によって ASP.NET ページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/ja-jp/library/zd1ysf1y.aspx
[方法: プログラムによってページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/ja-jp/library/z852zf6b.aspx
[Azure のキャッシュ サービス (プレビュー)]: http://go.microsoft.com/fwlink/?LinkId=320830
[キャッシュ サービス (プレビュー)]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache ディレクティブに関するページ]: http://go.microsoft.com/fwlink/?LinkId=251979
[Azure のキャッシュ サービス (プレビュー) の ClientDiagnosticLevel について]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet パッケージ マネージャーのインストールのページ]: http://go.microsoft.com/fwlink/?LinkId=240311
[キャッシュの料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/cache/
[管理ポータル]: https://manage.windowsazure.com/
[Azure のキャッシュ サービス (プレビュー) のキャッシュ オファリング]: http://go.microsoft.com/fwlink/?LinkId=317277
[Azure のキャッシュ サービス (プレビュー) の容量計画]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure のキャッシュ サービス (プレビュー) の有効期限と削除]: http://go.microsoft.com/fwlink/?LinkId=317278
[高可用性]: http://go.microsoft.com/fwlink/?LinkId=317329
[Azure のキャッシュ サービス (プレビュー) の通知]: http://go.microsoft.com/fwlink/?LinkId=317276
[Azure のキャッシュ サービス (プレビュー) への移行]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure のキャッシュ サービス (プレビュー) のサンプル]: http://go.microsoft.com/fwlink/?LinkId=320840

