<properties 
	pageTitle="Azure Redis Cache の FAQ | Microsoft Azure" 
	description="Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="sdanie"/>

# Azure Redis Cache の FAQ

Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。


## ここに質問の答えがない場合はどうすればいいですか。

質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

-	この FAQ の最後に掲載されている[Disqus スレッド](#comments)に質問を投稿し、Azure Cache チームや他のコミュニティ メンバーと この記事についてやり取りすることができます。
-	さらに多くの人と情報交換する場合は、[Azure Cache MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache)に質問を投稿すれば、Azure Cache チームや他のコミュニティ メンバーとやり取りすることができます。
-	機能要求を作成する場合は、要求とアイデアを [Azure Redis Cache のユーザーの声](https://feedback.azure.com/forums/169382-cache)に送信することができます。
-	また、[Azure Cache 外部フィードバック](mailto:azurecache@microsoft.com)にメールをお送りいただくこともできます。

## Azure Redis Cache の基本

このセクションの FAQ では、Azure Redis Cache の基本について説明します。

-    [Azure Redis Cache とは](#what-is-azure-redis-cache)
-    [Azure Redis Cache を開始するにはどうすればよいですか。](#how-can-i-get-started-with-azure-redis-cache)

次の FAQ は、Azure Redis Cache に関する基本的な概念と質問で、回答は他の FAQ セクションに示されています。

-	[Redis Cache のサービス内容と適切なサイズの選択](#what-redis-cache-offering-and-size-should-i-use)
-	[どのような Redis キャッシュ クライアントを使用できますか。](#what-redis-cache-clients-can-i-use)
-	[Azure Redis Cache のローカル エミュレーターがない場合](#is-there-a-local-emulator-for-azure-redis-cache)
-	[キャッシュの正常性とパフォーマンスの監視方法](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## 計画に関する FAQ

-	[Redis Cache のサービス内容と適切なサイズの選択](#what-redis-cache-offering-and-size-should-i-use)
-	[Azure Redis Cache のパフォーマンス](#azure-redis-cache-performance)
-	[キャッシュを配置するリージョン](#in-what-region-should-i-locate-my-cache)
-	[Azure Redis Cache の課金方法を教えてください。](#how-am-i-billed-for-azure-redis-cache)



## 開発に関する FAQ

-	[StackExchange.Redis 構成オプションについて](#what-do-the-stackexchangeredis-configuration-options-do)
-	[どのような Redis キャッシュ クライアントを使用できますか。](#what-redis-cache-clients-can-i-use)
-	[Azure Redis Cache のローカル エミュレーターがない場合](#is-there-a-local-emulator-for-azure-redis-cache)
-	[Redis コマンドの実行方法](#how-can-i-run-redis-commands)
-	[他のいくつかの Azure サービスと異なり Azure Redis Cache の MSDN クラス ライブラリ リファレンスが提供されない理由](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-	[Azure Redis Cache を PHP セッションのキャッシュとして使用できますか。](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## セキュリティに関する FAQ

-	[Redis への接続に非 SSL ポートを有効にする必要がある状況](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## 運用に関する FAQ

-	[いくつかの運用上のベスト プラクティスについて](#what-are-some-production-best-practices)
-	[一般的な Redis コマンドの使用に関するいくつかの考慮事項](#what-are-some-of-the-considerations-whja-JPing-common-redis-commands)
-	[キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-	[ThreadPool 拡大の重要な詳細情報](#important-details-about-threadpool-growth)
-	[StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる](#enable-server-gc-to-get-more-throughput-on-the-client-whja-JPing-stackexchangeredis)


## 監視とトラブルシューティングに関する FAQ

このセクションの FAQ では、監視とトラブルシューティングに関する一般的な質問について説明します。Azure Redis Cache インスタンスの監視とトラブルシューティングの詳細については、「[Azure Redis Cache の監視方法](cache-how-to-monitor.md)」および「[Azure Redis Cache のトラブルシューティング方法](cache-how-to-troubleshoot.md)」を参照してください。

-	[キャッシュの正常性とパフォーマンスの監視方法](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-	[キャッシュ診断ストレージ アカウントの設定を変更した場合](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-	[新しいキャッシュの診断を有効にして、それ以外は有効にしない理由](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-	[タイムアウトが発生する理由](#why-am-i-seeing-timeouts)
-	[クライアントがキャッシュから切断される理由](#why-was-my-client-disconnected-from-the-cache)


## 事前のキャッシュ オファリングに関する FAQ

-	[どの Azure Cache を利用すればよいですか。](#which-azure-cache-offering-is-right-for-me)


### Azure Redis Cache とは

Azure Redis Cache は広く普及しているオープン ソース [Redis Cache](http://redis.io) を基盤にしています。これを使用すると、Microsoft によって管理されている、セキュリティで保護された専用 Redis Cache に Azure 内の任意のアプリケーションからアクセスできます。詳細については、azure.com の [Azure Redis Cache](https://azure.microsoft.com/services/cache/) の製品ページを参照してください。


### Azure Redis Cache を開始するにはどうすればよいですか。

Azure Redis Cache を開始する方法はいくつかあります。

-    [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、[ASP.NET](cache-web-app-howto.md)、[Java](cache-java-get-started.md)、[Node.js](cache-nodejs-get-started.md)、[Python](cache-python-get-started.md) で使用可能なチュートリアルのいずれかを確認できます。
-    [Microsoft Azure Redis Cache を使用して高パフォーマンス アプリケーションを構築する方法](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)のビデオを見ることができます。
-    プロジェクトの開発言語と一致するクライアントのドキュメントで、Redis を使用する方法を確認できます。Azure Redis Cache で使用できる Redis クライアントは多数あります。Redis クライアントの一覧については、[http://redis.io/clients](http://redis.io/clients) を参照してください。


Azure アカウントをお持ちでない場合は、次の操作を行います。

-    [無料で Azure アカウントを開きます](/pricing/free-trial/?WT.mc_id=redis_cache_hero)。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、無料の Azure サービスと機能をご利用になれます。
-    [Visual Studio サブスクライバーの特典を有効にします](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。


<a name="cache-size"></a>
### Redis Cache のサービス内容と適切なサイズの選択
Azure Redis Cache には、さまざまなレベルの**サイズ**、**帯域幅**、**高可用性**、**SLA** オプションが用意されています。

Cache のオプションを選択するときの考慮事項を次に示します。

-	**メモリ**: Basic レベルと Standard レベルでは、250 MB ～ 53 GB です。Premium レベルは最大 530 GB であり、[要求すれば](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)さらに多く使用できます。詳細については、「[Azure Redis Cache の価格](https://azure.microsoft.com/pricing/details/cache/)」を参照してください。
-	**ネットワーク パフォーマンス**: 高いスループットを必要とするワークロードがある場合、Standard または Basic より Premium レベルの方が多くの帯域幅を提供します。また、各レベル内では、キャッシュをホストする基盤の VM のため、キャッシュのサイズが大きいほど帯域幅も増えます。詳細については[後の表](#cache-performance)を参照してください。
-	**スループット**: Premium レベルでは、使用可能な最大のスループットが提供されます。キャッシュ サーバーまたはクライアントが帯域幅の限界に達した場合、クライアント側でタイムアウトが発生します。詳細については、後の表を参照してください。
-	**高可用性/SLA**: Azure Redis Cache は、Standard/Premium キャッシュについて 99.9% の可用性を保証します。SLA の詳細については、「[Azure Redis Cache の価格](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)」を参照してください。SLA は、Cache エンドポイントへの接続のみをカバーします。SLA は、データ損失からの保護には対応していません。Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。
-	**Redis データの保持**: Premium レベルでは、Azure Storage アカウント内のキャッシュ データを永続化できます。Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。基盤となるインフラストラクチャに問題が発生した場合、データが失われる可能性があります。Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。Azure Redis Cache には、Redis の永続化の RDB オプションと AOF オプション (近日公開予定) があります。詳細については、「[Premium Azure Redis Cache の永続化の構成方法](cache-how-to-premium-persistence.md)」を参照してください。
-	**Redis クラスター**: 53 GB を超えるキャッシュを作成するか、複数の Redis ノード間でデータを共有するには、Premium レベルで利用可能な Redis クラスタリングを使用することができます。各ノードは、高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。詳細については、「[Premium Azure Redis Cache のクラスタリングの構成方法](cache-how-to-premium-clustering.md)」を参照してください。
-	**強化されたセキュリティとネットワーク分離**: Azure Virtual Network (VNET) のデプロイメントでは、Azure Redis Cache のための強化されたセキュリティと分離、およびサブネット、アクセス制御ポリシー、さらにアクセスを制限するためのその他の機能が提供されます。詳細については、「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」を参照してください。
-	**Redis の構成**: Standard レベルと Premium レベルでは、Keyspace 通知用に Redis を構成できます。
-	**最大クライアント接続数**: Premium レベルでは、Redis に接続できるクライアントの最大数が提供されます。キャッシュのサイズが大きいほど、接続の数は多くなります。[詳細については、料金のページを参照してください](https://azure.microsoft.com/pricing/details/cache/)。
-	**Redis サーバー専用コア**: Premium レベルでは、すべてのキャッシュ サイズに Redis 専用のコアがあります。Basic/Standard レベルでは、C1 サイズ以上に Redis サーバー専用コアがあります。
-	**Redis はシングル スレッドです**。したがって、3 つ以上のコアを使用しても 2 つのコアを使用する場合と比べて追加のメリットはありません。ただし、一般に、VM のサイズが大きいほど、小さなサイズよりも多くの帯域幅を利用できます。キャッシュ サーバーまたはクライアントが帯域幅の制限に達すると、クライアント側でタイムアウトが発生します。
-	**パフォーマンス向上**: Premium レベルのキャッシュは、高速プロセッサを備え、Basic/Standard レベルと比較して優れたパフォーマンスを発揮するハードウェア上にデプロイされます。Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。

<a name="cache-performance"></a>
### Azure Redis Cache のパフォーマンス

次の表に、Azure Redis Cache のエンドポイントに対して Iaas VM から `redis-benchmark.exe` を使用して Standard および Premium キャッシュのさまざまなサイズをテストした結果得られた最大帯域幅を示します。これらの値は保証された値ではなく、これらの値の SLA もありません。これらの値は、標準的な値と考えてください。アプリケーションに最適なキャッシュ サイズを特定するには、アプリケーションに対してロード テストを実行する必要があります。

この表からは次のような結論が得られます。

-	キャッシュのサイズが同じ場合のスループットは、Standard レベルより Premium レベルの方が高くなります。たとえば、6 GB のキャッシュの場合、P1 のスループットが 140 K RPS であるのに対し、C3 では 49 K になります。
-	Redis クラスタリングでは、クラスターのシャード (ノード) の数を増やすと、スループットもそれに比例して増加する。たとえば、10 シャードの P4 クラスターを作成した場合、使用可能なスループットは 250 万 (250 K * 10) RPS となります。
-	キー サイズを大きくしたときのスループットは、Standard レベルより Premium レベルのほうが高い。

| [価格レベル] | サイズ | CPU コア数 | 使用可能な帯域幅 | 1 KB のキーのサイズ |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Standard のキャッシュ サイズ** | | | **メガビット/秒 (Mb/s) / メガバイト/秒 (MB/s)** | **1 秒あたりの要求数 (RPS)** |
| C0 | 250 MB | 共有 | 5 / 0.625 | 600 |
| C1 | 1 GB | 1 | 100 / 12.5 | 12200 |
| C2 | 2\.5 GB | 2 | 200 / 25 | 24000 |
| C3 | 6 GB | 4 | 400 / 50 | 49000 |
| C4 | 13 GB | 2 | 500 / 62.5 | 61000 |
| C5 | 26 GB | 4 | 1000 / 125 | 115000 |
| C6 | 53 GB | 8 | 2000 / 250 | 150000 |
| **Premium のキャッシュ サイズ** | | **シャードあたりの CPU コア数** | | **1 秒あたりの要求数 (RPS)、シャードあたり** |
| P1 | 6 GB | 2 | 1000 / 125 | 140000 |
| P2 | 13 GB | 4 | 2000 / 250 | 220000 |
| P3 | 26 GB | 4 | 2000 / 250 | 220000 |
| P4 | 53 GB | 8 | 4000 / 500 | 250000 |


`redis-benchmark.exe` などの Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。

<a name="cache-region"></a>
### キャッシュを配置するリージョン

最適なパフォーマンスと最も短い待機時間を実現するには、キャッシュ クライアント アプリケーションと同じリージョンに Azure Redis Cache を配置します。

<a name="cache-billing"></a>
### Azure Redis Cache の課金方法を教えてください。

Azure Redis Cache の価格については、[ここ](https://azure.microsoft.com/pricing/details/cache/)を参照してください。価格ページには、1 時間単位の価格が表示されます。キャッシュは、キャッシュが作成された時間から削除された時間までの期間に関して、分単位で課金されます。キャッシュの課金を停止または一時停止するオプションはありません。


<a name="cache-configuration"></a>
### StackExchange.Redis 構成オプションについて

StackExchange.Redis には多くのオプションが用意されています。ここでは、いくつかの一般的な設定について説明します。StackExchange.Redis オプションの詳細については、[StackExchange.Redis の構成](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)に関するページを参照してください。

構成オプション|説明|推奨
---|---|---
AbortOnConnectFail|true の場合、ネットワーク障害の後に再接続が行われません。|StackExchange.Redis が自動的に再接続するように、false に設定します。
ConnectRetry|初期接続中に接続試行を繰り返す回数。| 次の注意事項を参考にしてください。 |
ConnectTimeout|接続操作のタイムアウト (ミリ秒単位)。| 次の注意事項を参考にしてください。 |

ほとんどの場合は、クライアントの既定値で十分です。ワークロードに基づいてオプションを微調整できます。

-	**再試行**
	-	ConnectRetry と ConnectTimeout に関する一般的なガイダンスは、早く失敗して再試行することです。これは、ワークロードと、クライアントが Redis コマンドを発行してから応答を受け取るまでに要する時間の平均に基づきます。
	-	自分で接続の状態を確認して再接続するのではなく、StackExchange.Redis が自動的に再接続するように設定します。**ConnectionMultiplexer.IsConnected プロパティは使用しません**。
	-	問題の肥大化 - ある問題が発生し、再試行しても問題が解決しないことがあります。そのような場合は、Microsoft Patterns & Practices グループ発行の[再試行に関する一般的なガイダンス](best-practices-retry-general.md)のページに説明されている指数バックオフ再試行アルゴリズムの使用を検討する必要があります。
-	**タイムアウト値**
	-	ワークロードを考慮したうえで値を適宜設定します。大きな値を保存する場合は、タイムアウトを大きな値に設定します。
	-	StackExchange.Redis が再接続できるように、`AbortOnConnectFail` を false に設定します。
	-	アプリケーションに対して ConnectionMultiplexer インスタンスを 1 つ使用します。 「[ConnectionMultiplexer クラスを使用してキャッシュに接続する](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)」に示されているように、LazyConnection を使用して、Connection プロパティから返される単一のインスタンスを作成できます。
	-	診断用には、`ConnectionMultiplexer.ClientName` プロパティを、アプリ インスタンスの一意な名前に設定します。
	-	カスタム ワークロードに対しては複数の `ConnectionMultiplexer` インスタンスを使用します。
	-	アプリケーションの負荷が変化する場合は、このモデルに従うことができます。次に例を示します。
	-	1 つのマルチプレクサーを使用して、サイズの大きなキーを処理できます。
	-	1 つのマルチプレクサーを使用して、サイズの小さなキーを処理できます。
	-	使用する ConnectionMultiplexer ごとに、異なる接続タイムアウト値と再試行ロジックを設定できます。
	-	診断を容易にするために、各マルチプレクサーで `ClientName` プロパティを設定します。
	-	これにより、`ConnectionMultiplexer` あたりの待機時間が合理化されます。

### どのような Redis キャッシュ クライアントを使用できますか。

Redis のメリットの 1 つが、クライアントが多数存在しており、さまざまな開発言語を多数サポートしている点です。現在のクライアントの一覧については、[Radis クライアント](http://redis.io/clients)に関するページをご覧ください。さまざまな言語とクライアントのチュートリアルについては、「[Azure Redis Cache の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)」の上部にある言語のタブで、目的の言語をクリックしてください。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### Azure Redis Cache のローカル エミュレーターがない場合

Azure Redis Cache のローカル エミュレーターがなくても、ローカル コンピューターの [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)から、redis-server.exe の MSOpenTech のバージョンを実行して接続し、以下の例のように、ローカル キャッシュ エミュレーターと同じような使い心地を得ることができます。


	private static Lazy<ConnectionMultiplexer>
  		lazyConnection = new Lazy<ConnectionMultiplexer>
	    (() =>
	    {
		    // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
		    return ConnectionMultiplexer.Connect("127.0.0.1:6379");
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
		    get
		    {
			    return lazyConnection.Value;
		    }
	    }


必要に応じて [redis.conf](http://redis.io/topics/config) ファイルを構成し、オンラインの Azure Redis Cache の[既定のキャッシュ設定](cache-configure.md#default-redis-server-configuration)とより厳密に一致させます。

<a name="cache-commands"></a>
### Redis コマンドの実行方法

「[Azure Redis Cache でサポートされない Redis コマンド](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)」で示されているコマンドを除き、[Redis コマンド](http://redis.io/commands#)のページに示されているすべてのコマンドを使用できます。Redis コマンドを実行するには、いくつかのオプションがあります。

-	Standard または Premium キャッシュがある場合は、[Redis コンソール](cache-configure.md#redis-console)を使用して Redis コマンドを実行できます。これは、Azure ポータルで Redis コマンドを安全に実行するための方法です。
-	Redis コマンド ライン ツールを使用することもできます。これらを使用するには、次の手順に従います。
-	[Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)をダウンロードします。
-	`redis-cli.exe` を使用してキャッシュに接続します。次の例に示すように、-h スイッチを使用してキャッシュ エンドポイントを渡し、-a を使用してキーを渡します。
-	`redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  -	Redis コマンド ライン ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、[Redis 向け ASP.NET セッション状態プロバイダー プレビュー リリースの発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)に関するブログ記事を参照してください。

<a name="cache-reference"></a>
### 他のいくつかの Azure サービスと異なり Azure Redis Cache の MSDN クラス ライブラリ リファレンスが提供されない理由

Microsoft Azure Redis Cache は、広く普及しているオープン ソースの Redis Cache をベースにしており、多くのプログラミングで使用可能なさまざまな [Redis クライアント](http://redis.io/clients)がアクセスできます。各クライアントは、[Redis コマンド](http://redis.io/commands)を使用して Redis キャッシュ インスタンスを呼び出す独自の API を持ちます。

クライアントはそれぞれ異なるため、MSDN には単独の一元的なクラス リファレンスは用意されていません。各クライアントで独自のリファレンス ドキュメントが管理されます。リファレンス ドキュメントのほかに、チュートリアルもいくつか用意されています。チュートリアルでは、さまざまな言語とキャッシュ クライアントを使用して Azure Redis Cache を使用する方法について説明します。こうしたチュートリアルにアクセスするには、「[Azure Redis Cache の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)」の上部にある言語のタブで、目的の言語をクリックしてください。


### Azure Redis Cache を PHP セッションのキャッシュとして使用できますか。

はい。Azure Redis Cache を PHP セッションのキャッシュとして使用するには、`session.save_path` に Azure Redis Cache インスタンスへの接続文字列を指定します。

>[AZURE.IMPORTANT] Azure Redis Cache を PHP セッションのキャッシュとして使用する場合、次の例に示すように、キャッシュへの接続に使用するセキュリティ キーを URL エンコードする必要があります。
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>キーが URL エンコードされていない場合、次のような例外が表示されます。`Failed to parse session.save_path`

Redis Cache を PhpRedis クライアントで PHP セッションのキャッシュとして使用する方法の詳細については、「[PHP Session handler (PHP セッション ハンドラー)](https://github.com/phpredis/phpredis#php-session-handler)」を参照してください。



<a name="cache-ssl"></a>
### Redis への接続に非 SSL ポートを有効にする必要がある状況

Redis サーバーは既定で SSL をサポートしませんが、Azure Redis Cache では SSL がサポートされます。Azure Redis Cache に接続するときに、クライアントが StackExchange.Redis のように SSL をサポートしている場合は、SSL を使用する必要があります。

既定では、新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています。クライアントが SSL をサポートしていない場合は、「[Azure Redis Cache でのキャッシュの構成](cache-configure.md)」の「[アクセス ポートへの](cache-configure.md#access-ports)」セクションの指示に従って、非 SSL ポートを有効にする必要があります。

`redis-cli` などの Redis ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、[Redis 向け ASP.NET セッション状態プロバイダー プレビュー リリースの発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)に関するブログ記事を参照してください。

Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。



### いくつかの運用上のベスト プラクティスについて

-	[StackExchange.Redis のベスト プラクティス](#stackexchangeredis-best-practices)
-	[構成と概念](#configuration-and-concepts)
-	[パフォーマンス テスト](#performance-testing)

#### StackExchange.Redis のベスト プラクティス

-	`AbortConnect` を "false" に設定してから、ConnectionMultiplexer による自動再接続を待ってください。[詳細についてはこちらをご覧ください](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)。
-	ConnectionMultiplexer は再利用し、要求ごとに新しく作成しないようにしてください。[こちらに記載されている](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) `Lazy<ConnectionMultiplexer>` パターンを強くお勧めします。
-	値が小さいほど Redis のパフォーマンスは向上するため、大きなデータは複数のキーに分割することを検討してください。[こちらの Redis に関する議論](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)では、100kb が "大きい" とみなされています。値が大きい場合に生じる可能性のある問題の例については、[こちらの記事](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size)を参照してください。
-	タイムアウトが起こらないように [ThreadPool の設定](#important-details-about-threadpool-growth)を構成してください。
-	connectTimeout については既定の 5 秒以上を使用してください。こうすることで、ネットワーク ブリップが発生した場合に、StackExchange.Redis で接続を再び確立するのに十分な時間を確保できます。
-	実行中のさまざまな操作に関連するパフォーマンス コストを把握してください。たとえば、`KEYS` コマンドは O(n) 操作であるため、使用しないでください。[redis.io のサイト](http://redis.io/commands/)に、Redis でサポートされる各操作の時間計算量の詳細が記載されています。各コマンドをクリックして、操作ごとの時間計算量を確認してください。

#### 構成と概念

-	実稼働システムでは Standard レベルまたは Premium レベルを使用する。Basic レベルは単一ノード システムであり、データ レプリケーション機能や SLA がありません。また、C1 以上のキャッシュを使用してください。C0 キャッシュは、単純な開発/テストシナリオ専用です。
-	Redis は**インメモリ** データ ストアであることに注意してください。[こちらの記事](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)を参照し、データが失われる可能性のあるシナリオについて把握してください。
-	[修正プログラムの適用やフェールオーバーによる](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)接続の中断に対応できるようなシステムを開発する。

#### パフォーマンス テスト

-	独自のパフォーマンス テストを作成する前に、`redis-benchmark.exe` を使用して実現可能なスループットを確認してください。Redis のベンチマークでは SSL はサポートされていないため、テストを行うには、[Azure ポータルで非 SSL ポートを有効にする](cache-configure.md#access-ports)必要があります。例については、「[キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)」を参照してください。
-	テストに使用するクライアント VM のリージョンは、Redis Cache インスタンスと同じものにする必要があります。
-	Dv2 VM シリーズはハードウェアが強力であり、最良の結果が得られるため、クライアントにはこれらのシリーズを使用することをお勧めします。
-	クライアント VM については、コンピューティング能力と帯域幅がテスト対象のキャッシュと同等以上であるものを選択してください。
-	Windows を使用している場合は、クライアント コンピューターで VRSS を有効にしてください。[詳細についてはこちらをご覧ください](https://technet.microsoft.com/library/dn383582.aspx)。
-	Premium レベルでは、Redis インスタンスは CPU およびネットワークの両方が優れたハードウェアで実行されるため、ネットワーク待ち時間およびスループットが改善します。

<a name="cache-redis-commands"></a>
### 一般的な Redis コマンドの使用に関するいくつかの考慮事項

-	処理に時間がかかる特定の Redis コマンドについては、その影響を理解せずに実行することは避けてください。
	-	たとえば、[KEYS](http://redis.io/commands/keys) コマンドは実稼働環境で実行しないでください。キーの数によっては、結果が返されるまでに長い時間がかかる場合があります。Redis はシングル スレッド サーバーであり、一度に 1 つずつコマンドを処理します。KEYS の後に他のコマンドが発行されている場合、それらのコマンドは Redis によって KEYS コマンドが処理されるまで処理されません。[redis.io のサイト](http://redis.io/commands/)に、Redis でサポートされる各操作の時間計算量の詳細が記載されています。各コマンドをクリックして、操作ごとの時間計算量を確認してください。
-	キー サイズ - 小さなキー/値と大きなキー/値のどちらを使用するか。 一般に、それはシナリオしだいです。サイズの大きなキーが必要となるシナリオでは、ConnectionTimeout 値、再試行回数、再試行ロジックを調整できます。Redis サーバーの観点からは、小さな値を設定した方がパフォーマンスが高くなります。
-	これは、サイズの大きな値を Redis に格納できないという意味ではありません。次の点に考慮する必要があります。待機時間は長くなります。サイズの大きなデータ セットとサイズの小さなデータ セットがある場合は、前の「[StackExchange.Redis 構成オプションについて](#cache-configuration)」に説明したように、それぞれ異なるタイムアウト値と再試行回数が構成された複数の ConnectionMultiplexer インスタンスを使用できます。



<a name="cache-benchmarking"></a>
### キャッシュのベンチマークを実行およびテストする方法

-	[キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。Azure ポータルでメトリックを表示できますが、任意のツールを使用して、メトリックを[ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)することも可能です。
-	redis-benchmark.exe を使用して Redis サーバーのロード テストを実行できます。
-	ロード テスト クライアントと Redis Cache が同じリージョン内にあることを確認します。
-	redis-cli.exe を使用し、INFO コマンドを使用してキャッシュを監視します。
-	負荷が高いことが原因でメモリの断片化が発生している場合は、キャッシュのサイズをスケールアップする必要があります。
-	Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands)」セクションを参照してください。

redis-benchmark.exe の使用例を次に示します。正確な結果を得るために、以下のコマンドはキャッシュと同じリージョンにある VM で実行してください。

-	1 k ペイロードを使用してパイプライン SET 要求をテストする

    redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50
	
-	1 k ペイロードを使用してパイプライン GET 要求をテストする。注: まず上記の SET テストを実行してキャッシュを設定してください。
	
    redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50

<a name="threadpool"></a>
### ThreadPool 拡大の重要な詳細情報

CLR ThreadPool には、2 種類のスレッド - 「Worker」と「I/O 完了ポート」 (別名 IOCP) スレッドがあります。

-	`Task.Run(…)` メソッドや `ThreadPool.QueueUserWorkItem(…)` メソッドの処理などには、Worker スレッドが使用されます。これらのスレッドは、バック グラウンド スレッドで作業が発生する必要がある場合に、CLR でさまざまなコンポーネントによっても使用されます。
-	IOCP スレッドは、非同期 IO が発生したときに使用されます(ネットワークからの読み取りなど)。

スレッド プールは、各種のスレッドについて「最小」設定に達するまで、新しい worker スレッドまたは I/O 完了スレッドをオンデマンドで (スロットルなしで) 提供します。既定では、スレッドの最小数はシステム上のプロセッサの数に設定されます。

既存の (ビジー) スレッドの数がスレッドの「最小」数に達すると、ThreadPool は新しいスレッドを挿入する速度を、500 ミリ秒ごとに 1 スレッドへとスロットルします。つまり、IOCP スレッドを必要とする作業のバーストをシステムが取得した場合、その作業を非常に高速に処理します。ただし、作業のバーストが構成済みの「最小」設定を超えた場合は、次の 2 つの状態のうちどちらかが発生するまで ThreadPool が待機するので、多少の遅延が生じます。

1. 既存のスレッドの 1 つが空き状態になり、作業を処理する。
1. 既存のスレッドが 500 ミリ秒間空き状態にならずに、新しいスレッドが作成される。

つまり、基本的には、ビジー状態のスレッド数が最小スレッド数よりも多い場合、ネットワーク トラフィックがアプリケーションによって処理されるまで 500 ミリ秒の遅延が発生すると考えられます。また、既存のスレッドが (私の記憶によると) 15 秒を超えてアイドル状態になると、そのスレッドはクリーンアップされ、拡大と縮小のこのサイクルが繰り返されることがあります。

StackExchange.Redis (ビルド 1.0.450 以降) からのエラー メッセージの例を見れば、ThreadPool の統計情報が出力されていることがわかります (IOCP と WORKER に関する下記の詳細を参照)。

	System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
	queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
	IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
	WORKER: (Busy=3,Free=997,Min=4,Max=1000)

上記の例では、IOCP スレッドには 6 つのビジー状態のスレッドがあり、システムで最小 4 つのスレッドを許可するように構成されていることがわかります。この場合、6 > 4 なので、クライアントでは 2 × 500 ミリ秒の遅延が発生したと考えられます。

IOCP スレッドまたは WORKER スレッドの拡大がスロットルされた場合、StackExchange.Redis がタイムアウトになる可能性があることに注意してください。

### 推奨

この情報に基づき、顧客が IOCP スレッドと WORKER スレッドの最小構成値を既定値よりも大きく設定することを強くお勧めします。この設定値については、1 つのアプリケーションで適切な値が別のアプリケーションでは高すぎる/低すぎるので、すべてのケースに対応できる案はありません。この設定は複雑なアプリケーションの他の部分のパフォーマンスにも影響を与える可能性があるので、各顧客は特定のニーズに合わせてこの設定を調整する必要があります。適切な値として、まず 200 または 300 に設定し、テストして必要に応じて調整します。

この設定を構成する方法

-	ASP.NET で、web.config の `<processModel>` 構成要素の下にある ["minIoThreads" 構成設定][]を使用します。Azure WebSites の内部で実行している場合、この設定は構成オプションを介して公開されません。ただし、これは global.asax.cs の Application\_Start メソッドからプログラムで設定できるはずです (下記を参照)。

> **重要な注意事項:** この構成要素で指定される値は、*"コアごと"* の設定となります。たとえば、4 コア マシンがあり、実行時の minIOThreads を 200 に設定する場合は、`<processModel minIoThreads="50"/>` を使用します。

-	ASP.NET の外部では、[ThreadPool.SetMinThreads(…)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API を使用します。

<a name="server-gc"></a>
### StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる

StackExchange.Redis を使用するときにサーバー GC を有効にすると、クライアントが最適化され、パフォーマンスとスループットを向上させることができます。サーバー GC とそれを有効にする方法の詳細については、次の記事を参照してください。

-	[サーバー GC を有効にするには](https://msdn.microsoft.com/library/ms229357.aspx)
-	[ガベージ コレクションの基礎](https://msdn.microsoft.com/library/ee787088.aspx)
-	[ガベージ コレクションとパフォーマンス](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### キャッシュの正常性とパフォーマンスの監視方法

Microsoft Azure Redis Cache のインスタンスは、[Azure ポータル](https://portal.azure.com)で監視できます。メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。詳細については、[Azure Redis Cache の監視](cache-how-to-monitor.md)に関するページをご覧ください。

Redis Cache の **[設定]** ブレードの **[サポート + トラブルシューティング]** セクションにも、キャッシュを監視およびトラブルシューティングするためのツールがいくつか用意されています。

-	**[トラブルシューティング]** では、一般的な問題と、その問題を解決するための戦略に関する情報を確認できます。
-	**[監査ログ]** では、キャッシュで実行される操作に関する情報を確認できます。また、フィルター処理を使用すれば、ビューを拡張してその他のリソースを含めることができます。
-	**[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。Azure Resource Health サービスの詳細については、「[Azure Resource Health の概要](../resource-health/resource-health-overview.md)」を参照してください。
-	**[新しいサポート要求]** には、キャッシュのサポート要求を開くためのオプションが用意されています。

これらのツールによって、Azure Redis Cache インスタンスの正常性を監視し、キャッシュ アプリケーションを管理できます。詳細については、「[サポートおよびトラブルシューティング設定](cache-configure.md#support-amp-troubleshooting-settings)」を参照してください。

### キャッシュ診断ストレージ アカウントの設定を変更した場合

同じリージョンやサブスクリプションにあるキャッシュは同じ診断ストレージ設定を共有するため、構成が変更される (診断が有効/無効に設定される、またはストレージ アカウントが変更される) と、その変更がそのリージョンにあるサブスクリプションのすべてのキャッシュに適用されます。キャッシュの診断設定が変更された場合は、同じサブスクリプションおよびリージョンの、別のキャッシュの診断設定が変更されたかどうかを確認します。確認する 1 つの方法として、`Write DiagnosticSettings` イベントのキャッシュの監査ログを表示します。監査ログの操作方法の詳細については、「[イベントと監査ログの表示](../azure-portal/insights-debugging-with-events.md)」と「[Resource Manager の監査操作](../resource-group-audit.md)」を参照してください。Azure Redis Cache イベントの監視の詳細については、「[処理とアラート](cache-how-to-monitor.md#operations-and-alerts)」を参照してください。

### 新しいキャッシュの診断を有効にして、それ以外は有効にしない理由

同じリージョンとサブスクリプションのキャッシュは、同じ診断ストレージの設定を共有します。新しいキャッシュを、別のキャッシュとして同じリージョンとサブスクリプションに作成し、診断を有効にすると、同じ設定が新しいキャッシュの診断で使用されます。


<a name="cache-timeouts"></a>
### タイムアウトが発生する理由

タイムアウトは、Redis との対話に使用されているクライアントで発生します。ほとんどの場合、Redis サーバーでタイムアウトが発生することはありません。Redis サーバーに送信されたコマンドは、キューに格納されます。コマンドは、最終的に Redis サーバーによって取得され、実行されます。ただし、この処理中にクライアントがタイムアウトすることがあり、その場合は呼び出し元では例外が発生します。タイムアウトの問題のトラブルシューティングについては、「[クライアント側のトラブルシューティング](cache-how-to-troubleshoot.md#client-side-troubleshooting)」および「[StackExchange.Redis のタイムアウトの例外](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions)」を参照してください。


<a name="cache-disconnect"></a>
### クライアントがキャッシュから切断される理由

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







### どの Azure Cache を利用すればよいですか。

>[AZURE.IMPORTANT]昨年[お知らせ](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)したとおり、Azure Managed Cache Service と Azure In-Role Cache サービスは 2016 年 11 月 30 日で提供が終了します。そのため、[Azure Redis Cache](https://azure.microsoft.com/services/cache/) を使用することをお勧めします。移行については、「[Managed Cache Service から Azure Redis Cache への移行](cache-migrate-to-redis.md)」をご覧ください。

### Azure Redis Cache
Azure Redis Cache は、最大 53 GB で一般公開されています。可用性の SLA は 99.9% です。新しい [Premium 階層](cache-premium-tier-intro.md)は、最大 530 GB のサイズを提供し、クラスタリング、VNET、および永続化を 99.9% の SLA でサポートします。

Azure Redis Cache では、Microsoft が管理する安全な専用 Redis Cache を利用できます。このサービスでは、Redis が提供する豊富な機能セットとエコシステムを利用し、Microsoft による信頼性の高いホスティングと監視を受けられます。

キーと値ペアのみを扱う従来のキャッシュとは異なり、Redis は高パフォーマンスな種類のデータに人気があります。また、Redis は、このようなデータに対するアトミックな操作 (文字列の付加、ハッシュ内の値のインクリメント、リストへのプッシュ、積集合、和集合、および差集合の計算、並べ替えられた集合内で最高ランクのメンバーの取得など) の実行もサポートしています。その他の機能として、トランザクションのサポート、パブリッシュ/サブスクライブ、Lua スクリプト、有効期限が制限されたキー、Redis を従来のキャッシュのように動作させるための構成設定があります。

Redis が正常に動作するために重要な点は、Redis を中心として正常に動作する強力なオープン ソース エコシステムが構築されていることです。また、その環境を複数の言語で使用できる多様な Redis クライアントに反映します。そうすることで、Azure 内に構築するほとんどすべてのワークロードに使用できるようになります。

Azure Redis Cache の使用方法については、「[Azure Redis Cache の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)」と [Azure Redis Cache のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/redis-cache/)を参照してください。

### Managed Cache Service
[Managed Cache サービスは 2016 年 11 月 30 日に終了となります。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### In-Role Cache
[In-Role Cache は 2016 年 11 月 30 日に終了となります。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





["minIoThreads" 構成設定]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx

<!---HONumber=AcomDC_0921_2016-->