<properties 
	pageTitle="Azure Redis Cache のサンプル" 
	description="Azure Redis Cache の使用方法について説明します" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Azure Redis Cache のサンプル 

このトピックでは、キャッシュへの接続、キャッシュ内のデータの読み取りと書き込み、ASP.NET Redis Cache プロバイダーの使用などのシナリオを含め、Azure Redis Cache のサンプルの一覧を示します。サンプルの一部はダウンロード可能なプロジェクトです。ステップ バイ ステップ ガイダンスが用意されているサンプルやコード スニペットが含まれているサンプルもありますが、ダウンロード可能なプロジェクトにはリンクしていません。

## Hello world サンプル

このセクションのサンプルでは、Azure Redis Cache インスタンスへの接続およびさまざまな言語と Redis クライアントを使用したキャッシュ内のデータの読み取りと書き込みの基本を示します。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルは [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET クライアントを利用してさまざまなキャッシュ操作を実行する方法を示しています。

このサンプルは次の方法を示しています。

-	さまざまな接続オプションを使用する
-	同期操作と非同期操作を使用し、キャッシュ間でオブジェクトを読み書きする
-	Redis MGET/MSET コマンドを使用し、指定されたキーの値を返す
-	Redis トランザクション操作を実行する
-	Redis リストと並べ替えられたセットを使用する
-	JsonConvert シリアライザーを使用し、.NET オブジェクトを保存する
-	Redis セットを使用し、タグ付けを実装する
-	Redis クラスターの操作

詳細については、github の「[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)」文書を参照してください。他の用途シナリオについては、「[StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests)」ユニット テストを参照してください。

「[Python で Azure Redis Cache を使用する方法](cache-python-get-started.md)」では、Python と [redis-py](https://github.com/andymccurdy/redis-py) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[PHP の例](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample)」では、PHP と [predis](https://github.com/nrk/predis) クライアントを使用して Azure Redis Cache を使用する方法を説明しています。

「[キャッシュ内で .NET オブジェクトを使用する](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects)」では、Azure Redis Cache インスタンスとの間でオブジェクトを読み書きできる、.NET オブジェクトをシリアル化する方法の 1 つを説明しています。

## ASP.NET SignalR のスケールアウト バックプレーンとしての Redis Cache の使用

「[ASP.NET SignalR のスケール アウト バックプレーンとしての Redis Cache の使用](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)」サンプルでは、SignalR のバックプレーンとして Azure Redis Cache を使用する方法について示しています。バックプレーンの詳細については、「[Redis を使用した SignalR のスケールアウト](http://www.asp.net/signalr/overview/performance/scaleout-with-redis)」を参照してください。

## Redis Cache の顧客クエリのサンプル

このサンプルでは、キャッシュ内のデータへのアクセスと永続ストレージ内のデータへのアクセスとのパフォーマンスを比較します。このサンプルには 2 つのプロジェクトがあります。

-	[Redis Cache でデータをキャッシュしてパフォーマンスを向上させる方法のデモ](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[デモ用のデータベースおよびキャッシュのシード](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## ASP.NET セッション状態と出力キャッシュ

「[Azure Redis Cache を使用した ASP.NET SessionState と OutputCache の格納](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching)」サンプルでは、Azure Redis Cache を使用して、Redis の SessionState プロバイダーと OutputCache プロバイダーで ASP.NET セッションと出力キャッシュを格納する方法を示しています。

## MAML を使用した Azure Redis Cache の管理

「[Azure 管理ライブラリを使用した Azure Redis Cache の管理](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)」サンプルでは、Azure 管理ライブラリを使用してキャッシュを管理 (作成/更新/削除) する方法を示しています。

## カスタム監視のサンプル

「[Redis Cache の監視データへのアクセス](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)」サンプルは、Azure ポータルの外部から Azure Redis Cache の監視データにアクセスする方法を示しています。

## PHP と Redis を使用して記述された Twitter 形式の複製

[Retwis](https://github.com/SyntaxC4-MSFT/retwis) サンプルは「Redis Hello World」です。[Predis](https://github.com/nrk/predis) クライアントを使用し、Redis と PHP で記述された最小の Twitter 形式のソーシャル ネットワーク クローンです。このソース コードは非常に単純に設計されており、同時にさまざまな Redis データ構造を示しています。

## 帯域幅モニター

「[帯域幅モニター](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor)」サンプルでは、クライアントで使用されている帯域幅を監視できます。帯域幅を測定するには、キャッシュ クライアント コンピューターでサンプルを実行し、キャッシュを呼び出し、帯域幅モニターのサンプルで報告された帯域幅を観察します。

<!---HONumber=AcomDC_0309_2016-->