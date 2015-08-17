<properties 
	pageTitle="Azure Redis Cache の FAQ" 
	description="Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。" 
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
	ms.date="07/24/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache の FAQ

Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。

<a name="cache-size"></a>
## Redis Cache のサービス内容と適切なサイズの選択

Azure Redis Cache には、さまざまなレベルの**サイズ**、**帯域幅**、**高可用性**、**SLA** オプションが用意されています。

-	Basic SKU - 単一ノード。レプリケーションまたは SLA なし。250 MB ～ 53 GB のキャッシュ サイズ。
-	Standard SKU - プライマリ/セカンダリ ノード (自動レプリケーションあり)。99.9% の SLA。250 MB ～ 53 GB のキャッシュ サイズ。

高可用性が必要な場合は、99.9% の SLA が保証される Standard キャッシュを選択してください。開発やプロトタイプの作成、または SLA が必要ないシナリオでは、Basic プランが適しています。

キャッシュ サイズと帯域幅は、キャッシュをホストする仮想マシンのサイズと帯域幅にほぼ対応します。Basic プランと Standard プランの 250 MB のサイズは、共有コアを使用してホストされる XS (A0) 仮想マシン サイズでホストされます。これ以外のサイズは、専用のコアを使用してホストされます。1 GB のキャッシュ サイズは、オペレーティング システムと Redis キャッシュの両方のサービスに使用される 1 つの専用仮想コアを持つ S (A1) 仮想マシン サイズでホストされます。さらにサイズが大きなキャッシュは、複数の専用仮想コアを持つより大きな VM インスタンスでホストされます。

キャッシュのスループットが高い場合は、1 GB 以上のサイズを選択します。この場合、キャッシュは専用のコアを使用して実行されます。1 GB のキャッシュ サイズは、シングル コアの仮想マシンでホストされます。このコアは、OS とキャッシュの両方のサービスに使用されます。1 GB を超えるキャッシュは複数のコアを持つ仮想マシンで実行されます。Redis キャッシュは、OS と共有されない専用のコアを使用します。

**Redis はシングル スレッドです**。したがって、3 つ以上のコアを使用しても 2 つのコアを使用する場合と比べて追加のメリットはありません。ただし、一般に、**VM のサイズが大きいほど、小さなサイズよりも多くの帯域幅を利用できます**。キャッシュ サーバーまたはクライアントが帯域幅の制限に達すると、クライアント側でタイムアウトが発生します。

次の表に、Azure Redis Cache のエンドポイントに対して Iaas VM から `redis-benchmark.exe` を使用して Azure Redis Cache のさまざまなサイズをテストした結果得られた最大帯域幅を示します。これらの値は保証された値ではなく、これらの値の SLA もありません。これらの値は、標準的な値と考えてください。アプリケーションに最適なキャッシュ サイズを特定するには、アプリケーションに対してロード テストを実行する必要があります。

キャッシュ名|キャッシュ サイズ|GET/秒 (1 KB の値の単純な GET 呼び出し)|帯域幅 (メガビット/秒)
---|---|---|---
C0|250 MB|610|5
C1|1 GB|12,200|100
C2|2\.5 GB|24,300|200
C3|6 GB|48,875|400
C4|13 GB|61,350|500
C5|26 GB|112,275|1,000
C6|53 GB|153,219|1,000+

`redis-benchmark.exe` などの Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。

<a name="cache-region"></a>
## キャッシュを配置するリージョン

最適なパフォーマンスと最も短い待機時間を実現するには、キャッシュ クライアント アプリケーションと同じリージョンに Azure Redis Cache を配置します。

<a name="cache-billing"></a>
## Azure Redis Cache の課金方法を教えてください。

Azure Redis Cache の価格については、[ここ](http://azure.microsoft.com/pricing/details/cache/)を参照してください。料金ページには、1 時間単位の料金が表示されます。キャッシュは、キャッシュが作成された時間から削除された時間までの期間に関して、分単位で課金されます。キャッシュの課金を停止または一時停止するオプションはありません。

<a name="cache-timeouts"></a>
## タイムアウトが発生する理由

タイムアウトは、Redis との対話に使用されているクライアントで発生します。ほとんどの場合、Redis サーバーでタイムアウトが発生することはありません。Redis サーバーに送信されたコマンドは、キューに格納されます。コマンドは、最終的に Redis サーバーによって取得され、実行されます。ただし、この処理中にクライアントがタイムアウトすることがあり、その場合は呼び出し元では例外が発生します。タイムアウトの問題のトラブルシューティングの詳細については、[Azure Redis Cache の StackExchange.Redis のタイムアウトの例外の調査](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/)に関するページを参照してください。

<a name="cache-monitor"></a>
## キャッシュの正常性とパフォーマンスの監視方法

Microsoft Azure Redis Cache のインスタンスは、[Azure プレビュー ポータル](https://portal.azure.com)で監視できます。メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。これらのツールによって、Azure Redis Cache インスタンスの正常性を監視し、キャッシュ アプリケーションを管理できます。キャッシュの監視の詳細については、「[Azure Redis Cache の監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)」を参照してください。

<a name="cache-disconnect"></a>
## クライアントがキャッシュから切断される理由

キャッシュが切断される一般的な理由のいくつかを次に示します。

-	クライアント側の原因
	-	クライアント アプリケーションが再デプロイされた。
	-	クライアント アプリケーションがスケーリング操作を実行した。
		-	Cloud Services または Web Apps の場合、自動スケールが原因になっている場合があります。
	-	クライアント側のネットワーク レイヤーが変更された。
	-	クライアントで、またはクライアントとサーバー間のネットワーク ノードで一時的なエラーが発生した。
	-	帯域幅のしきい値制限に達した。
	-	CPU バインド型の操作の完了に時間がかかった。
-	サーバー側の原因
	-	Standard キャッシュ プランで、Azure Redis Cache Service がプライマリ ノードからセカンダリ ノードへのフェールオーバーを開始した。
	-	Azure により、キャッシュがデプロイされているインスタンスに修正プログラムが適用されていた。
		-	Redis サーバーの更新または VM の一般的なメンテナンスの場合もこれに該当します。

<a name="cache-configuration"></a>
## StackExchange.Redis 構成オプションについて

StackExchange.Redis には多くのオプションが用意されています。ここでは、いくつかの一般的な設定について説明します。StackExchange.Redis オプションの詳細については、[StackExchange.Redis の構成](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)に関するページを参照してください。

構成オプション|説明|推奨
---|---|---
AbortOnConnectFail|true の場合、ネットワーク障害の後に再接続が行われません。|StackExchange.Redis が自動的に再接続するように、false に設定します。
ConnectRetry|初期接続中に接続試行を繰り返す回数。||
ConnectTimeout|接続操作のタイムアウト (ミリ秒単位)。|

ほとんどの場合は、クライアントの既定値で十分です。ワークロードに基づいてオプションを微調整できます。

-	再試行
	-	ConnectRetry と ConnectTimeout に関する一般的なガイダンスは、早く失敗して再試行することです。これは、ワークロードと、クライアントが Redis コマンドを発行してから応答を受け取るまでに要する時間の平均に基づきます。
	-	自分で接続の状態を確認して再接続するのではなく、StackExchange.Redis が自動的に再接続するように設定します。**ConnectionMultiplexer.IsConnected プロパティは使用しません**。
	-	問題の肥大化 - ある問題が発生し、再試行しても問題が解決しないことがあります。そのような場合は、Microsoft Patterns & Practices グループ発行の[再試行に関する一般的なガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md)のページに説明されている指数バックオフ再試行アルゴリズムの使用を検討する必要があります。
-	タイムアウト値
	-	ワークロードを考慮したうえで値を適宜設定します。大きな値を保存する場合は、タイムアウトを大きな値に設定します。
		-	StackExchange.Redis が自動的に再接続するように、ABortOnConnectFail を false に設定します。
-	アプリケーションに対して ConnectionMultiplexer インスタンスを 1 つ使用します。 「[ConnectionMultiplexer クラスを使用してキャッシュに接続する](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect)」に示されているように、LazyConnection を使用して、Connection プロパティから返される単一のインスタンスを作成できます。
-	診断用には、`ConnectionMultiplexer.ClientName` プロパティを、アプリ インスタンスの一意な名前に設定します。
-	カスタム ワークロードに対しては複数の `ConnectionMultiplexer` インスタンスを使用します。
	-	アプリケーションの負荷が変化する場合は、このモデルに従うことができます。次に例を示します。
		-	1 つのマルチプレクサーを使用して、サイズの大きなキーを処理できます。 
		-	1 つのマルチプレクサーを使用して、サイズの小さなキーを処理できます。 
		-	使用する ConnectionMultiplexer ごとに、異なる接続タイムアウト値と再試行ロジックを設定できます。
		-	診断を容易にするために、各マルチプレクサーで `ClientName` プロパティを設定します。 
		-	これにより、`ConnectionMultiplexer` あたりの待機時間が合理化されます。

<a name="cache-redis-commands"></a>
## 一般的な Redis コマンドの使用に関するいくつかの考慮事項

-	処理に時間がかかる特定の Redis コマンドについては、その影響を理解せずに実行することは避けてください。
	-	たとえば、[KEYS](http://redis.io/commands/keys) コマンドは実稼働環境で実行しないでください。キーの数によっては、結果が返されるまでに長い時間がかかる場合があります。Redis はシングル スレッド サーバーであり、一度に 1 つずつコマンドを処理します。KEYS の後に他のコマンドが発行されている場合、それらのコマンドは Redis によって KEYS コマンドが処理されるまで処理されません。
-	キー サイズ - 小さなキー/値と大きなキー/値のどちらを使用するか。 一般に、それはシナリオしだいです。サイズの大きなキーが必要となるシナリオでは、ConnectionTimeout 値、再試行回数、再試行ロジックを調整できます。Redis サーバーの観点からは、小さな値を設定した方がパフォーマンスが高くなります。
	-	これは、サイズの大きな値を Redis に格納できないという意味ではありません。次の点に考慮する必要があります。待機時間は長くなります。サイズの大きなデータ セットとサイズの小さなデータ セットがある場合は、前の「[StackExchange.Redis 構成オプションについて](#cache-configuration)」に説明したように、それぞれ異なるタイムアウト値と再試行回数が構成された複数の ConnectionMultiplexer インスタンスを使用できます。


<a name="cache-ssl"></a>
## Redis への接続に非 SSL ポートを有効にする必要がある状況

Redis サーバーは既定で SSL をサポートしませんが、Azure Redis Cache では SSL がサポートされます。Azure Redis Cache に接続するときに、クライアントが StackExchange.Redis のように SSL をサポートしている場合は、SSL を使用する必要があります。

既定では、新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています。クライアントが SSL をサポートしていない場合は、「[Azure Redis Cache でのキャッシュの構成](https://msdn.microsoft.com/library/azure/dn793612.aspx)」の「[アクセス ポートへの](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts)」セクションの指示に従って、非 SSL ポートを有効にする必要があります。

`redis-cli` などの Redis ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、[Redis 向け ASP.NET セッション状態プロバイダー プレビュー リリースの発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)に関するブログ記事を参照してください。

Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。

<a name="cache-benchmarking"></a>
## キャッシュのベンチマークを実行およびテストする方法

-	[キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。ポータルではメトリックを表示できますが、お好みのツールを使用し、それを[ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)することも可能です。
-	redis-benchmark.exe を使用して Redis サーバーのロード テストを実行できます。
	-	ロード テスト クライアントと Redis キャッシュが同じリージョン内にあることを確認します。
-	redis-cli.exe を使用し、INFO コマンドを使用してキャッシュを監視します。
	-	負荷が高いことが原因でメモリの断片化が発生している場合は、キャッシュのサイズを大きくする必要があります。
-	Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。

<a name="cache-commands"></a>
## Redis コマンドの実行方法

[Azure Redis Cache でサポートされない Redis コマンド](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)のページに示されているコマンドを除いて、[Redis コマンド](http://redis.io/commands#)のページに示されているすべてのコマンドを使用できます。Redis コマンドを実行するには、いくつかのオプションがあります。

-	Standard キャッシュがある場合は、[Redis コンソール](cache-configure.md#redis-console)を使用して Redis コマンドを実行できます。これは、Azure ポータルで Redis コマンドを安全に実行するための方法です。
-	Redis コマンド ライン ツールを使用することもできます。これらを使用するには、次の手順に従います。
	-	[Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)をダウンロードします。
	-	`redis-cli.exe` を使用してキャッシュに接続します。次の例に示すように、-h スイッチを使用してキャッシュ エンドポイントを渡し、-a を使用してキーを渡します。
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Redis コマンド ライン ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、[Redis 向け ASP.NET セッション状態プロバイダー プレビュー リリースの発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)に関するブログ記事を参照してください。

<a name="cache-common-patterns"></a>
## いくつかの一般的なキャッシュ パターンと考慮事項

-	Microsoft Patterns & Practices から次のガイダンスが公開されています。
	-	[キャッシュに関するガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)。
	-	[Azure クラウド アプリケーションの設計および実装に関するガイダンス](https://github.com/mspnp/azure-guidance)
-	[Azure Redis Cache の一般的なキャッシュ パターン](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## 他のいくつかの Azure サービスと異なり Azure Redis Cache の MSDN クラス ライブラリ リファレンスが提供されない理由

Microsoft Azure Redis Cache は、広く普及しているオープン ソースの Redis Cache をベースに、Microsoft によって管理された、セキュリティで保護された専用の Redis キャッシュへのアクセスを提供します。[Redis クライアント](http://redis.io/clients)についても、多くのプログラミング言語に対応してさまざまなクライアントが提供されています。各クライアントは、[Redis コマンド](http://redis.io/commands)を使用して Redis キャッシュ インスタンスを呼び出す独自の API を持ちます。

クライアントはそれぞれ異なるため、MSDN には単独の一元的なクラス リファレンスは用意されていません。各クライアントで独自のリファレンス ドキュメントが管理されます。リファレンス ドキュメントに加えて、Azure.com の[ Redis Cache のドキュメント](http://azure.microsoft.com/documentatgion/services/redis-cache/)のページには、さまざまな言語およびキャッシュ クライアントを使用して Azure Redis Cache を使用する方法に関するいくつかのチュートリアルがあります。

<!---HONumber=August15_HO6-->