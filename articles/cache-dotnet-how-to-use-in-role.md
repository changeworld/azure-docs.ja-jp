<properties urlDisplayName="Caching" pageTitle="インロール キャッシュの使用方法 (.NET) - Azure の機能ガイド" metaKeywords="Azure cache, Azure caching, Azure cache, Azure caching, Azure store session state, Azure cache .NET, Azure cache C#" description="Azure インロール キャッシュの使用方法をについて説明します。サンプルは C# コードで記述され、.NET API を利用しています。" metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use In-Role Cache for Azure Cache" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="cache" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/18/2014" ms.author="sdanie" />






# Azure のキャッシュのインロール キャッシュの使用方法

このガイドでは、**Windows Azure のキャッシュのインロール キャッシュ** 
**Azure Cache のインロール キャッシュ**サンプルは C\# コードで記述され、.NET API を利用しています。紹介するシナリオは、**キャッシュ クラスターの構成**、**キャッシュ クライアントの構成**、**キャッシュでのオブジェクトの追加と削除、キャッシュへの ASP.NET セッション状態の格納**、**キャッシュの使用による ASP.NET ページ出力キャッシュの有効化**などです。インロール キャッシュの使い方の詳細については、「[次の手順][]」を参照してください。

>アプリケーションに適した Azure のキャッシュ オファリングを選択する方法については、「[どの Azure Cache を利用すればよいですか。][]」を参照してください。

## 目次

-   [インロール キャッシュとは][]
-	[インロール キャッシュの概要]
	-	[キャッシュ クラスターの構成][]
	-	[キャッシュ クライアントの構成][]
-	[キャッシュの操作][]
	-	[方法:DataCache オブジェクトを作成する][]
	-   [方法:キャッシュでオブジェクトを追加および削除する][]
	-   [方法:キャッシュ内のオブジェクトの有効期限を指定する][]
	-   [方法:ASP.NET セッション状態をキャッシュに格納する][]
	-   [方法:ASP.NET ページ出力キャッシュをキャッシュに格納する][]
-   [次のステップ][]

<a name="what-is"></a>
## インロール キャッシュとは

インロール キャッシュは、Azure アプリケーションにキャッシュ レイヤーを提供します。Caching を使用すると、他のバックエンド ソースから情報を一時的にメモリ内に格納することでパフォーマンスが向上します。さらに、クラウドでのデータベース トランザクションに関連するコストを削減できます。インロール キャッシュには、次のような機能があります。

-   セッション状態とページ出力キャッシュに対応した、構築済みの ASP.NET プロバイダーにより、アプリケーション コードを変更しなくても Web アプリケーションの機能を強化できます。
-   任意のシリアル化可能なマネージ オブジェクトをキャッシュできます。たとえば、CLR オブジェクト、行、XML、バイナリ データなどです。
-   Azure と Windows Server AppFabric の両方で、開発モデルの一貫性を確保できます。

インロール キャッシュでは、Azure クラウド サービス (ホステッド サービスとも呼ばれます) のロール インスタンスをホストしている仮想マシンのメモリの一部を使用してキャッシュを行う新しい方法が導入されました。展開のオプションの点で柔軟性が向上し、キャッシュのサイズは非常に大きくすることができ、キャッシュ固有のクォータの制限がなくなりました。

ロール インスタンスでのキャッシュには、次のような利点があります。

-	キャッシュのための追加料金が必要ない。キャッシュをホストするコンピューティング リソースの分だけをお支払いいただきます。
-	キャッシュのクォータおよび調整が不要になる。
-	より高度な制御と分離を行うことができる。 
-	パフォーマンスが向上する。
-	ロールがスケールインまたはスケールアウトするときに、自動的にキャッシュのサイズが調整される。ロール インスタンスが追加または削除されるときに、キャッシュのために利用できるメモリの規模が効率的に拡大または縮小されます。
-	開発時デバッグを完全な忠実度で行うことができる。 
-	memcache プロトコルがサポートされる。

また、ロール インスタンスでのキャッシュには、次のような構成オプションがあります。

-	キャッシュ専用のロールを構成するか、キャッシュを既存のロールに配置する。 
-	同じクラウド サービス デプロイ内の複数のクライアントがキャッシュを利用できるようにする。
-	異なるプロパティを持つ複数の名前付きキャッシュを作成する。
-	必要に応じて個々のキャッシュに高可用性を構成する。
-	リージョン、タグ付け、通知などの拡張キャッシュ機能を使用する。

このガイドでは、インロール キャッシュの基本的な概要について説明します。この概要ガイドでは扱われていない機能の詳細については、[インロール キャッシュの概要に関するページ][]を参照してください。

<a name="getting-started-cache-role-instance"></a>
## インロール キャッシュの概要

インロール キャッシュは、ロール インスタンスをホストしている仮想マシン上のメモリを使用して、キャッシュを利用できるようにします。キャッシュをホストするロール インスタンスは、**キャッシュ クラスター**と呼ばれます。ロール インスタンスでのキャッシュには、次の 2 つの展開トポロジがあります。

-	**専用ロール** キャッシュ - ロール インスタンスがキャッシュだけのために使用されます。
-	**併置ロール** キャッシュ - キャッシュは VM リソース (帯域幅、CPU、およびメモリ) をアプリケーションと共有します。

ロール インスタンスでキャッシュを使用するには、キャッシュ クラスターを構成してから、キャッシュ クラスターにアクセスできるようにキャッシュ クライアントを構成する必要があります。

-	[キャッシュ クラスターの構成][]
-	[キャッシュ クライアントの構成][]

<a name="enable-caching"></a>
## キャッシュ クラスターの構成

**併置ロール** キャッシュ クラスターを構成するには、キャッシュ クラスターをホストするロールを選択します。**ソリューション エクスプローラー**でロール プロパティを右クリックし、**[プロパティ]** をクリックします。

![RoleCache1][RoleCache1]

**[キャッシュ]** タブに切り替えて、**[キャッシュを有効にする]** チェック ボックスをオンにし、必要なキャッシュ オプションを指定します。**ワーカー ロール**または **ASP.NET Web ロール**でキャッシュが有効になっている場合、既定の構成は**併置ロール** キャッシュで、ロール インスタンスのメモリの 30% がキャッシュ用に割り当てられています。既定のキャッシュは自動的に構成され、必要であれば追加の名前付きキャッシュを作成できます。これらのキャッシュは、割り当てられたメモリを共有します。

![RoleCache2][RoleCache2]

**専用ロール** キャッシュ クラスターを構成するには、プロジェクトに**キャッシュ ワーカー ロール**を追加します。

![RoleCache7][RoleCache7]

**キャッシュ ワーカー ロール**がプロジェクトに追加される場合、既定の構成は**専用ロール キャッシュ**です。

![RoleCache8][RoleCache8]

キャッシュが有効になると、キャッシュ クラスターのストレージ アカウントを構成できるようになります。インロール キャッシュには、Azure のストレージ アカウントが必要です。このストレージ アカウントは、キャッシュ クラスターを構成するすべての仮想マシンからアクセスされる、キャッシュ クラスターについての構成データを保持するために使用されます。このストレージ アカウントの指定は、キャッシュ クラスター ロールのプロパティ ページの **[キャッシュ]** タブで、**[名前付きキャッシュの設定]** のすぐ上で行います。

![RoleCache10][RoleCache10]

>このストレージ アカウントが構成されていない場合、ロールは起動できません。 

キャッシュのサイズは、ロールの VM サイズ、ロールのインスタンス数、およびキャッシュ クラスターが専用ロールと併置ロールのどちらとして構成されているかの組み合わせによって決まります。

>このセクションでは、キャッシュ サイズの構成についての簡単な概要を示します。キャッシュ サイズとその他の容量計画の考慮事項の詳細については、[インロール キャッシュの容量計画に関する考慮事項のページ][]を参照してください。

仮想マシン サイズとロール インスタンス数を構成するには、**ソリューション エクスプローラー**でロール プロパティを右クリックし、**[プロパティ]** をクリックします。

![RoleCache1][RoleCache1]

**[構成]** タブに切り替えます。既定の**インスタンス数**は 1 で、既定の **VM サイズ**は **S** です。

![RoleCache3][RoleCache3]

VM サイズの合計メモリは、次のとおりです。 

-	**S**:1.75 GB
-	**M**:3.5 GB
-	**L**:7 GB
-	**XL**:14 GB


> これらのメモリ サイズは、OS、キャッシュ プロセス、キャッシュ データ、およびアプリケーション間で共有される、VM で利用できるメモリの合計量を表しています。仮想マシン サイズの構成の詳細については、「[仮想マシンのサイズの構成方法][]」を参照してください。キャッシュは **XS** VM サイズではサポートされていないことに注意してください。

**併置ロール** キャッシュが指定された場合、キャッシュ サイズは仮想マシン メモリの指定された割合によって決定されます。**専用ロール** キャッシュが指定された場合、仮想マシンの利用可能なすべてのメモリがキャッシュに使用されます。2 つのロール インスタンスが構成されている場合は、仮想マシンの合計されたメモリが使用されます。このようにキャッシュ クラスターが形成され、利用可能なキャッシュ メモリは複数のロール インスタンスに分散されているものの、キャッシュのクライアントには 1 つのリソースとして提示されます。追加のロール インスタンスを構成すると、同じようにキャッシュ サイズが増加します。希望するサイズのキャッシュをプロビジョニングするために必要な設定を判断するには、[インロール キャッシュの容量計画に関する考慮事項のページ][]で示されている容量計画スプレッドシートを使用できます。

キャッシュ クラスターの構成が済んだら、キャッシュにアクセスできるようにキャッシュ クライアントを構成できます。

<a name="NuGet"></a>
## キャッシュ クライアントの構成

インロール キャッシュのキャッシュにアクセスするには、クライアントが同じ展開内になければなりません。キャッシュ クラスターが専用ロール キャッシュ クラスターである場合、クライアントは展開内の他のロールです。キャッシュ クラスターが併置ロール キャッシュ クラスターである場合、クライアントは展開内の他のロールであるか、キャッシュ クラスターをホストするロール自体です。キャッシュにアクセスする各クライアント ロールを構成するために使用できる NuGet パッケージが用意されています。Caching NuGet パッケージを使用して、キャッシュ クラスターにアクセスするようにロールを構成するには、**ソリューション エクスプローラー**でロール プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

![RoleCache4][RoleCache4]

**[インロール キャッシュ]** を選択し、**[インストール]**、**[同意する]** の順にクリックします。

>一覧に **[インロール キャッシュ]** が表示されない場合は、**[オンライン検索]** ボックスに「**WindowsAzure.Caching**」と入力し、結果の中から選択します。

![RoleCache5][RoleCache5]

NuGet パッケージは、いくつかの処理を行います。必要な構成をロールの config ファイルに追加したり、キャッシュ クライアントの診断レベル設定を Azure アプリケーションの ServiceConfiguration.cscfg ファイルに追加したり、必要なアセンブリ参照を追加したりします。

>ASP.NET Web ロールでは、Caching NuGet パッケージは 2 つのコメント アウトされたセクションも web.config に追加します。1 つ目のセクションはセッション状態をキャッシュに格納できるようにし、2 つ目のセクションは ASP.NET ページがキャッシュを出力できるようにします。詳細については、「[方法:ASP.NET セッション状態をキャッシュに格納する]」および「[方法:ASP.NET ページ出力キャッシュをキャッシュに格納する][]」

NuGet パッケージは、次の構成要素をロールの web.config または app.config に追加します。**dataCacheClients** セクションおよび **cacheDiagnostics** セクションは、**configSections** 要素の下に追加されます。**configSections** 要素が存在しない場合は、**configuration** 要素の子として作成されます。

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

これらの新しいセクションには、**dataCacheClients** 要素および **cacheDiagnostics** 要素への参照が含まれています。 これらの要素も **configuration** 要素に追加されます。

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

構成が追加された後で、**[cache cluster role name]** を、キャッシュ クラスターをホストするロールの名前に置き換えます。

>**[[cache cluster role name]]** を、キャッシュ クラスターをホストするロールの名前に置き換えないと、キャッシュがアクセスされたときに **TargetInvocationException** がスローされます。その内部に、"そのようなロールは存在しません" というメッセージを持つ **DatacacheException** が含まれています。

NuGet パッケージは、キャッシュ クライアント ロールの ServiceConfiguration.cscfg 内の **ConfigurationSettings** に、**ClientDiagnosticLevel** 設定も追加します。次の例は、ServiceConfiguration.cscfg ファイルの **WebRole1** セクションで、**ClientDiagnosticLevel** は 1 になっています (**ClientDiagnosticLevel** の既定値)。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>インロール キャッシュでは、キャッシュ サーバーとキャッシュ クライアントの両方の診断レベルが用意されています。診断レベルは、キャッシュのために収集される診断情報のレベルを構成する 1 つの設定です。詳細については、[インロール キャッシュのトラブルシューティングと診断に関するページ][]を参照してください。

NuGet パッケージは、次のアセンブリへの参照も追加します。

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

ロールが ASP.NET Web ロールである場合は、次のアセンブリ参照も追加されます。

-	Microsoft.Web.DistributedCache.dll

>これらのアセンブリは、C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\2012-10\\ref\\Caching\\ フォルダーにあります。

クライアント プロジェクトをキャッシュ用に構成できたら、以降のセクションで説明されている、キャッシュを操作するための技法を使用できます。

<a name="working-with-caches"></a>
## キャッシュの操作

このセクションの手順では、キャッシュに対する一般的なタスクを行う方法について説明します。

-	[方法:DataCache オブジェクトを作成する][]
-   [方法:キャッシュでオブジェクトを追加および削除する][]
-   [方法:キャッシュ内のオブジェクトの有効期限を指定する][]
-   [方法:ASP.NET セッション状態をキャッシュに格納する][]
-   [方法:ASP.NET ページ出力キャッシュをキャッシュに格納する][]

<a name="create-cache-object"></a>
## 方法:DataCache オブジェクトを作成する

プログラムでキャッシュを操作するには、キャッシュへの参照が必要です。使用するファイルの先頭に、次の内容を追加します。
インロール キャッシュ:

    using Microsoft.ApplicationServer.Caching;

>Cache NuGet パッケージをインストールして、必要な参照が追加された後も、Visual Studio が using ステートメント内の型を認識しない場合は、
プロジェクトのターゲット プロファイルが .NET Framework 4 以降であることを確認し、
**クライアント プロファイル**と指定されていないプロファイルを選択するようにしてください。キャッシュ クライアントを構成する手順については、「[キャッシュ クライアントの構成][]」を参照してください。

**DataCache** オブジェクトを作成する方法は、2 つあります。最初の方法では、目的のキャッシュの名前を渡して、単純に **DataCache** を作成します。

    DataCache cache = new DataCache("default");

**DataCache** がインスタンス化されたら、以降のセクションで説明されているように、それを使ってキャッシュを操作できます。

2 番目の方法では、既定のコンストラクターを使用して、アプリケーション内に新しい **DataCacheFactory** オブジェクトを作成します。このようにすると、キャッシュ クライアントが構成ファイル内の設定を使うようになります。**DataCache** オブジェクトを返す、新しい **DataCacheFactory** インスタンスの **GetDefaultCache** メソッドを呼び出すか、**GetCache** メソッドを呼び出してキャッシュの名前を渡します。これらのメソッドは、プログラムでキャッシュにアクセスするために使用できる **DataCache** オブジェクトを返します。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## 方法:キャッシュでオブジェクトを追加および削除する

キャッシュに項目を追加するには、**Add** メソッドまたは **Put** メソッドを使用できます。**Add** メソッドは、指定されたオブジェクトを、キー パラメーターの値でキー付けして、キャッシュに追加します。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

キャッシュに同じキーのオブジェクトが既にある場合、**DataCacheException** がスローされ、次のメッセージが表示されます。

> ErrorCode:SubStatus:キャッシュに既に存在するキーを使用してオブジェクトを作成しようとしています。キャッシュは、
> オブジェクトに対して一意のキーの値のみを受け入れます。

特定のキーのオブジェクトを取得するには、**Get** メソッドを使用できます。オブジェクトが存在する場合はそれが返され、存在しない場合は null が返されます。

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

**Put** メソッドは、指定されたキーを持つオブジェクトがキャッシュに存在しなければそれを追加し、存在すればそのオブジェクトを置き換えます。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 方法:キャッシュ内のオブジェクトの有効期限を指定する

既定では、キャッシュ内の項目はキャッシュに置かれてから 10 分で期限切れになります。これは、キャッシュ クラスターをホストしているロールのロール プロパティの **[Time to Live (分)]** 設定で構成できます。

![RoleCache6][RoleCache6]

**[有効期限の種類]** には、次の 3 つがあります。**[なし]**、**[絶対]**、および **[スライディング ウィンドウ]** です。これらは、**[Time to Live (分)]** を使用して有効期限を決定する方法を構成します。既定の **[有効期限の種類]** は **[絶対]** であり、キャッシュの項目の有効期限のカウントダウン タイマーは、キャッシュに項目が置かれたときに始まります。指定された時間が経過すると、項目は期限切れになります。**[スライディング ウィンドウ]** が指定されている場合、項目の有効期限のカウントダウンはキャッシュ内の項目がアクセスされるたびにリセットされ、最後のアクセス以降に指定の時間が経過するまでは期限切れになりません。**[なし]** が指定されている場合、**[Time to Live (分)]** は **0** に設定されている必要があります。項目は期限切れにならずに、キャッシュ内にある限り有効のままになります。

ロール プロパティで構成されているよりも長いまたは短いタイムアウト間隔にする場合は、キャッシュ内の項目を追加または更新するときに、**TimeSpan** パラメーターを取る **Add** および **Put** のオーバーロードを使用して、特定の期間を指定できます。次の例では、文字列 **value** が **item** でキー付けされ、30 分のタイムアウトでキャッシュに追加されます。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

キャッシュ内の項目の残りのタイムアウト間隔を確認するには、**GetCacheItem** メソッドを使用して、キャッシュ内の項目についての情報 (残りのタイムアウト間隔など) が含まれている **DataCacheItem** オブジェクトを取得できます。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 方法:ASP.NET セッション状態をキャッシュに格納する

インロール キャッシュのセッション状態プロバイダーは、ASP.NET アプリケーション用のプロセス外ストレージ メカニズムです。このプロバイダーを使用すると、セッション状態をメモリ内や SQL Server データベース内ではなく、Azure のキャッシュ内に格納できます。キャッシュ セッション状態プロバイダーを使用するには、まずキャッシュ クラスターを構成し、次に「[インロール キャッシュの概要][]」で説明されているように Caching NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。Caching NuGet パッケージをインストールすると、コメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションがインロール キャッシュ用のセッション状態プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use In-Role Cache for session state caching
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

>Caching NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストール][]のページからインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。

インロール キャッシュ用のセッション状態プロバイダーを有効にするには、指定されたセクションをコメント解除します。既定のキャッシュは、提供されるスニペットで指定されています。別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

Caching サービス セッション状態プロバイダーの使い方の詳細については、[インロール キャッシュのセッション状態プロバイダーに関するページ][]を参照してください。

<a name="store-page"></a>
## 方法:ASP.NET ページ出力キャッシュをキャッシュに格納する

インロール キャッシュの出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。このデータは、完全な HTTP 応答専用です (ページ出力キャッシュ)。プロバイダーは、ASP.NET 4 で導入された新しい出力キャッシュ プロバイダー拡張機能ポイントに接続します。出力キャッシュ プロバイダーを使用するには、まずキャッシュ クラスターを構成し、次に「[インロール キャッシュの概要][]」で説明されているように Caching NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。Caching NuGet パッケージをインストールすると、次のコメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションがインロール キャッシュ用の出力キャッシュ プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use In-Role Cache for output caching
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

>Caching NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストール][]のページからインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。

インロール キャッシュ用の出力キャッシュ プロバイダーを有効にするには、指定されたセクションをコメント解除します。既定のキャッシュは、提供されるスニペットで指定されています。別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

出力をキャッシュする各ページに、**OutputCache** ディレクティブを追加します。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

この例では、キャッシュされたページ データが 60 秒間キャッシュに保持され、パラメーターの組み合わせごとに異なるバージョンのページがキャッシュされます。使用できるオプションの詳細については、[OutputCache ディレクティブに関するページ][]を参照してください。

インロール キャッシュの出力キャッシュ プロバイダーの使い方の詳細については、[インロール キャッシュの出力キャッシュ プロバイダーに関するページ][]を参照してください。

<a name="next-steps"></a>
## 次のステップ

ここまでで、インロール キャッシュの基本について説明しました。
次のリンクでは、さらに複雑なキャッシュ タスクを実行する方法について説明します。

-   MSDN リファレンス:[インロール キャッシュ](http://go.microsoft.com/fwlink/?LinkId=252658)
-   インロール キャッシュへの移行方法を確認する:[Windows Azure のキャッシュのインロール キャッシュへの移行][]
-   サンプルをチェックする:[インロール キャッシュのサンプル (Windows Azure のキャッシュ)][]
-	TechEd 2013 の「[最大のパフォーマンス: Azure の Caching によってクラウド サービス アプリケーションを高速化する][]」セッションで、インロール キャッシュに関する説明を参照してください。

<!-- INTRA-TOPIC LINKS -->
[次の手順]: #next-steps
[次のステップ]: #next-steps
[インロール キャッシュとは]: #what-is
[Azure Cache の作成]: #create-cache
[最適なキャッシュの種類]: #choosing-cache
[インロール キャッシュ サービスの概要]: #getting-started-cache-service
[インロール キャッシュを使用するための Visual Studio プロジェクトの準備]: #prepare-vs
[Caching を使用するようにアプリケーションを構成する]: #configure-app
[インロール キャッシュの概要]: #getting-started-cache-role-instance
[キャッシュ クラスターの構成]: #enable-caching
[必要なキャッシュ サイズの構成]: #cache-size
[キャッシュ クライアントの構成]: #NuGet
[キャッシュの操作]: #working-with-caches
[方法:DataCache オブジェクトを作成する]: #create-cache-object
[方法:キャッシュでオブジェクトを追加および削除する]: #add-object
[方法:キャッシュ内のオブジェクトの有効期限を指定する]: #specify-expiration
[方法:ASP.NET セッション状態をキャッシュに格納する]: #store-session
[方法:ASP.NET ページ出力キャッシュをキャッシュに格納する]: #store-page
[サポートされている .NET Framework プロファイル]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[仮想マシンのサイズの構成方法]: http://go.microsoft.com/fwlink/?LinkId=164387
[方法:プログラムを使ってキャッシュ クライアントを構成する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg618003.aspx
[方法:プログラムによってページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/ja-jp/library/z852zf6b.aspx
[方法:宣言によって ASP.NET ページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/ja-jp/library/zd1ysf1y.aspx
[インロール キャッシュの容量計画に関する考慮事項のページ]: http://go.microsoft.com/fwlink/?LinkId=252651
[インロール キャッシュのサンプル (Windows Azure のキャッシュ)]: http://msdn.microsoft.com/ja-jp/library/jj189876.aspx
[インロール キャッシュ_remove]: http://go.microsoft.com/fwlink/?LinkId=252658
[インロール キャッシュ_remove]: http://www.microsoft.com/ja-jp/showcase/Search.aspx?phrase=azure+caching
[最大のパフォーマンス: Azure の Caching によってクラウド サービス アプリケーションを高速化する]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Windows Azure のキャッシュのインロール キャッシュへの移行]: http://msdn.microsoft.com/ja-jp/library/hh914163.aspx
[NuGet パッケージ マネージャーのインストール]: http://go.microsoft.com/fwlink/?LinkId=240311
[インロール キャッシュの出力キャッシュ プロバイダーに関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185662.aspx
[OutputCache ディレクティブに関するページ]: http://go.microsoft.com/fwlink/?LinkId=251979
[インロール キャッシュの概要に関するページ]: http://go.microsoft.com/fwlink/?LinkId=254172
[インロール キャッシュのセッション状態プロバイダーに関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185668.aspx
[チーム ブログ]: http://blogs.msdn.com/b/windowsazure/
[インロール キャッシュのトラブルシューティングと診断に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache:セッション状態のキャッシュ]: http://www.microsoft.com/ja-jp/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure 管理ポータル]: http://windows.azure.com/
[Azure Shared Caching]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg278356.aspx

[どの Azure Cache を利用すればよいですか。]: http://msdn.microsoft.com/ja-jp/library/azure/dn766201.aspx

<!--HONumber=35.2-->
