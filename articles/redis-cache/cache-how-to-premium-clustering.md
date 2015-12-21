<properties 
	pageTitle="Premium Azure Redis Cache の Redis クラスタリングの構成方法" 
	description="Premium レベルの Azure Redis Cache インスタンス用に Redis を作成して管理する方法について説明します" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Premium Azure Redis Cache の Redis クラスタリングの構成方法
Azure Redis Cache には、新しい Premium レベルなど、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。

Azure Redis Cache の Premium レベルには、クラスタリング、永続性、および Virtual Network のサポートが含まれています。この記事では、Premium Azure Redis Cache インスタンスでクラスタリングを構成する方法について説明します。

その他の Premium キャッシュ機能については、「[Premium Azure Redis Cache の永続化の構成方法](cache-how-to-premium-persistence.md)」および「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」を参照してください。

## Redis クラスターとは
Azure Redis Cache では、[Redis での実装](http://redis.io/topics/cluster-tutorial)と同じように Redis クラスターが提供されます。Redis クラスターには次の利点があります。

-	データセットを複数のノードに自動的に分割する機能。 
-	ノードのサブセットで障害が発生したとき、クラスターの他の部分と通信できないときに、操作を続行する機能。 
-	より多くのスループット: シャードの数を増やすと、スループットは比例して増加します。 
-	より多くのメモリ サイズ: シャードの数を増やすと比例的に増加します。  

Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Redis Cache の FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」を参照してください。

Azure では、Redis クラスターは、各シャードがプライマリ/レプリカ ペアを持つプライマリ/レプリカ モデルとして提供され、レプリケーションは Azure Redis Cache Service によって管理されます。

## クラスタリング
クラスタリングは、キャッシュの作成中に **[Redis Cache の新規作成]** ブレードで構成します。キャッシュを作成するには、[Azure ポータル](https://portal.azure.com)にサインインし、**[新規]**、**[データ + ストレージ]**、**[Redis Cache]** の順にクリックします。

![Redis Cache の作成][redis-cache-new-cache-menu]

クラスタリングを構成するには、まず **[価格レベルの選択]** ブレードで **[Premium]** キャッシュのいずれかを選択します。

![価格レベルの選択][redis-cache-premium-pricing-tier]

クラスタリングは **[Redis クラスター]** ブレードで構成します。

![クラスタリング][redis-cache-clustering]

クラスター内に最大 10 個のシャードを作成できます。**[有効]** をクリックし、クスライダーを操作するか 1 ～ 10 の値を入力して **[シャード数]** を設定し、**[OK]** をクリックします。

各シャードは Azure によって管理されるプライマリ/レプリカ キャッシュ ペアであり、キャッシュの合計サイズはシャードの数に価格レベルで選択したキャッシュ サイズを掛けることによって計算されます。

![クラスタリング][redis-cache-clustering-selected]

キャッシュを作成した後は、クラスター化されていないキャッシュと同じようにアクセスして使用でき、Redis はキャッシュのシャード全体にデータを分配します。診断が[有効](cache-how-to-monitor.md#enable-cache-diagnostics)になっている場合は、シャードごとにメトリックが個別にキャプチャされ、Redis Cache ブレードに[表示](cache-how-to-monitor.md)できます。

>[AZURE.IMPORTANT]StackExchange.Redis を使用して、クラスタリングが有効になっている Azure Redis Cache に接続すると、問題が発生し、`MOVE` 例外がスローされる場合があります。これは、StackExchange.Redis キャッシュ クライアントがキャッシュ クラスターのノードに関する情報を収集するのに若干時間がかかるためです。これらの例外は、キャッシュに初めて接続し、クライアントがこの情報の収集を終了する前に、すぐにキャッシュを呼び出した場合に発生する可能性があります。アプリケーションでこの問題を解決する最も簡単な方法として、キャッシュに接続した後、1 秒間待機してからキャッシュを呼び出します。これを行うには、次のサンプル コードに示すように、`Thread.Sleep(1000)` を追加します。`Thread.Sleep(1000)` が発生するのは、キャッシュへの最初の接続時だけであることに注意してください。詳細については、「[StackExchange.Redis.RedisServerException - MOVED #248](https://github.com/StackExchange/StackExchange.Redis/issues/248)」をご覧ください。この問題の修正プログラムの開発が進められており、更新プログラムをここに投稿する予定です。

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
        // Connect to the Redis cache for the first time
	    var connection =  ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

		// Wait for 1 second
		Thread.Sleep(1000);

		// Return the connected ConnectionMultiplexer
		return connection;
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## クラスタリングの FAQ

次の一覧は、Azure Redis Cache のクラスタリングに関するよく寄せられる質問への回答です。

## クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか

-	クラスタリングが有効になっている場合は、データベース 0 のみを使用できます。クライアント アプリケーションで複数のデータベースを使用している状態で、0 以外のデータベースに対して読み取りまたは書き込みを試みると、次の例外がスローされます。`Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
-	[StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) を使用する場合は、1.0.481 以降を使用する必要があります。クラスタリングが有効になっていないキャッシュに接続するときに使用するものと同じ[エンドポイント、ポート、キー](cache-configure.md#properties)を使用して、キャッシュに接続します。唯一の違いは、すべての読み取りと書き込みをデータベース 0 に対して実行する必要があることです。
	-	他のクライアントの要件は異なる場合があります。「[すべての Redis クライアントがクラスタリングをサポートしますか](#do-all-redis-clients-support-clustering)」を参照してください。
-	アプリケーションで 1 つのコマンドにバッチ処理された複数のキー操作を使用する場合は、すべてのキーを同じシャードに配置する必要があります。このようにする場合は、「[クラスターにはキーはどのように配布されるのですか](#how-are-keys-distributed-in-a-cluster)」を参照してください。
-	Redis ASP.NET セッション状態プロバイダーを使用する場合は、2.0.0 以降を使用する必要があります。「[Redis ASP.NET セッション状態および出力キャッシュ プロバイダーでクラスタリングを使用できますか](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)」を参照してください。

## クラスターにはキーはどのように配布されるのですか

Redis の[キー配布モデル](http://redis.io/topics/cluster-spec#keys-distribution-model)に関するドキュメントによると、キー スペースは 16384 スロットに分割されます。各キーはハッシュされ、クラスターのノード全体に配布される、これらのスロットのいずれかに割り当てられます。ハッシュ タグを使用して同じシャードに複数のキーが配置されていることを確認するために、キーのどの部分をハッシュするかを構成することができます。

-	ハッシュ タグのあるキー - キーの任意の部分を `{` と `}` で囲むと、キーのその部分のみが、キーのハッシュ スロットを決定するためにハッシュされます。たとえば、`{key}1`、`{key}2`、`{key}3` という 3 つのキーは、名前の `key` 部分のみがハッシュされるため、同じシャードに配置されます。キーのハッシュ タグ仕様に関する完全なリストについては、「[キーのハッシュ タグ](http://redis.io/topics/cluster-spec#keys-hash-tags)」を参照してください。
-	ハッシュ タグのないキー - キー名全体がハッシュに使用されます。そのため、キャッシュのシャード全体で統計的に均等に配布されます。

最高のパフォーマンスとスループットを得るために、キーを均等に配布することをお勧めします。ハッシュ タグのあるキーを使用する場合、キーが均等に配布されていることを確認するのはアプリケーションの責任です。

詳細については、「[キー配布モデル](http://redis.io/topics/cluster-spec#keys-distribution-model)」、「[Redis クラスターのデータ シャーディング](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)」および「[キーのハッシュ タグ](http://redis.io/topics/cluster-spec#keys-hash-tags)」を参照してください。

## 作成できる最大キャッシュ サイズはどれくらいですか

Premium の最大キャッシュ サイズは、53 GB です。最大 10 個のシャードを作成できるので、最大サイズは 530 GB です。さらに大きいサイズが必要な場合は、[追加を要求](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)できます。詳細については、「[Azure Redis Cache の価格](https://azure.microsoft.com/pricing/details/cache/)」を参照してください。

## すべての Redis クライアントがクラスタリングをサポートしますか

現時点では、すべてのクライアントが Redis クラスタリングをサポートしているわけではありません。StackExchange.Redis はサポートしているものの 1 つです。他のクライアントの詳細については、[Redis クラスター チュートリアル](http://redis.io/topics/cluster-tutorial)の「[クラスターの使用](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」を参照してください。

>[AZURE.NOTE]StackExchange.Redis をクライアントとして使用する場合は、クラスタリングが正常に動作するように、[StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 以降の最新バージョンを使用してください。

## クラスタリングが有効になっているとき、キャッシュに接続するにはどうすればよいですか

クラスタリングが有効になっていないキャッシュに接続するときに使用するものと同じ[エンドポイント、ポート、キー](cache-configure.md#properties)を使用して、キャッシュに接続できます。Redis がバックエンドのクラスタリングを管理するので、クライアントから管理する必要はありません。

## キャッシュの個々のシャードに直接接続できますか

これは公式にはサポートされていません。つまり、各シャードはキャッシュ インスタンスと総称して呼ばれるプライマリ/レプリカ キャッシュ ペアで構成されます。GitHub で Redis リポジトリの[不安定な](http://redis.io/download)ブランチにある redis-cli ユーティリティを使用して、これらのキャッシュ インスタンスに接続できます。このバージョンは、`-c` スイッチ付きで起動した場合、基本的なサポートを実装しています。詳細については、[http://redis.io](http://redis.io) の [Redis クラスター チュートリアル](http://redis.io/topics/cluster-tutorial)で「[クラスターの使用](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」を参照してください。

SSL 以外の場合は、次のコマンドを使用します。

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

SSL の場合は、`1300N` を `1500N` に置き換えます。

## 以前に作成したキャッシュのクラスタリングを構成できますか。

現時点では、クラスタリングを有効にして構成できるのは、キャッシュを作成するときだけです。

## Basic または Standard キャッシュのクラスタリングを構成できますか。

クラスタリングは、Premium キャッシュでのみ使用できます。

## Redis ASP.NET セッション状態および出力キャッシュ プロバイダーでクラスタリングを使用できますか

-	**Redis 出力キャッシュ プロバイダー** - 変更する必要はありません。
-	**Redis セッション状態プロバイダー** - クラスタリングを使用するには、[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.0 以降を使用する必要があります。そうしないと、例外がスローされます。これは重大な変更です。詳細については、「[v2.0.0 の重大な変更の詳細](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)」を参照してください。

## 次のステップ
Premium キャッシュ機能をさらに使用する方法を学習します。

-	[How to configure persistence for a Premium Azure Redis Cache (Premium Azure Redis Cache の永続性の構成方法)](cache-how-to-premium-persistence.md)
-	[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=AcomDC_1210_2015-->