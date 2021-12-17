---
title: Azure Cache for Redis のサンプル
description: コード サンプル (キャッシュへの接続、キャッシュに対するデータの読み取りと書き込み、ASP.NET Azure Cache for Redis プロバイダー) での Azure Cache for Redis の使用方法について説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 899bf97fc638fa2a763013319e517b76cd37d361
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537632"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis のサンプル
この記事では、Azure Cache for Redis サンプルの一覧をご覧いただけます。 このサンプルでは、次のようなシナリオを取り上げます。 

* キャッシュに接続する
* キャッシュとの間でデータの読み書きを行う
* ASP.NET Azure Cache for Redis プロバイダーを使用する。 

一部のサンプルは、ダウンロード可能なプロジェクトです。 その他のサンプルで、コード スニペットを含むステップバイステップのガイドが提供されますが、ダウンロード可能なプロジェクトにはリンクされていません。

## <a name="hello-world-samples"></a>Hello world サンプル
このセクションのサンプルでは、Azure Cache for Redis インスタンスへの接続の基本を示します。 サンプルでは、さまざまな言語と Redis クライアントを使用したキャッシュへのデータの読み取りと書き込みも示します。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルは、[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET クライアントを使用してさまざまなキャッシュ操作を実行する方法を示しています。

このサンプルは次の方法を示しています。

* さまざまな接続オプションを使用する
* 同期操作と非同期操作を使用し、キャッシュ間でオブジェクトを読み書きする
* Redis MGET/MSET コマンドを使用し、指定されたキーの値を返す
* Redis トランザクション操作を実行する
* Redis リストと並べ替えられたセットを使用する
* JsonConvert シリアライザーを使用し、.NET オブジェクトを保存する
* Redis セットを使用し、タグ付けを実装する
* Redis クラスターの操作

詳細については、GitHub の [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) ドキュメントを参照してください。 その他の使用シナリオについては、[StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) 単体テストを参照してください。

[Python で Azure Cache for Redis を使用する方法](cache-python-get-started.md)に関するページでは、Python と [redis-py](https://github.com/andymccurdy/redis-py) クライアントを使用して Azure Cache for Redis の使用を開始する方法を説明しています。

「[キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」には、.NET オブジェクトをシリアル化し、それらのオブジェクトを Azure Cache for Redis インスタンスとの間で読み書きする 1 つの方法が説明されています。 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>ASP.NET SignalR のスケールアウト バックプレーンとしての Azure Cache for Redis の使用
[ASP.NET SignalR のスケール アウト バックプレーンとして Azure Cache for Redis を使用する](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)サンプルは、SignalR のバックプレーンとして Azure Cache for Redis を使用する方法を説明しています。 バックプレーンの詳細については、「 [Redis を使用した SignalR のスケールアウト](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)」を参照してください。

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache for Redis の顧客クエリのサンプル
このサンプルでは、キャッシュ内のデータへのアクセスと永続ストレージ内のデータへのアクセスのパフォーマンスを比較しています。 このサンプルには 2 つのプロジェクトがあります。

* [Azure Cache for Redis でデータをキャッシュしてパフォーマンスを向上させる方法のデモ](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [デモ用のデータベースおよびキャッシュのシード](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET セッション状態と出力キャッシュ
[Azure Cache for Redis を使用して ASP.NET SessionState と OutputCache を格納する](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching)サンプルは、以下を説明しています。  

* Azure Cache for Redis を使用して ASP.NET セッションと出力キャッシュを格納する方法
* Redis の SessionState プロバイダーと OutputCache プロバイダーを使用する。

## <a name="manage-azure-cache-for-redis-with-maml"></a>MAML で Azure Cache for Redis を管理する
[Azure 管理ライブラリを使用して Azure Cache for Redis を管理する](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)サンプルは、Azure 管理ライブラリを使用してキャッシュを管理 (作成、更新、削除) する方法を説明しています。 

## <a name="custom-monitoring-sample"></a>カスタム監視のサンプル
[Azure Cache for Redis の監視データにアクセスする](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)サンプルは、Azure portal の外部から Azure Cache for Redis の監視データにアクセスする方法を説明しています。

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>PHP と Redis を使用して記述された Twitter 形式の複製
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) サンプルは「Redis Hello World」です。 これは、[Predis](https://github.com/nrk/predis) クライアントを使用し、Redis と PHP で記述された最小の Twitter 形式のソーシャル ネットワーク クローンです。 シンプルであると同時に、さまざまな Redis データ構造を示すようにソース コードが設計されています。

## <a name="bandwidth-monitor"></a>帯域幅モニター
「 [帯域幅モニター](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) 」サンプルでは、クライアントで使用されている帯域幅を監視できます。 帯域幅を測定するには、キャッシュ クライアント コンピューターでサンプルを実行し、キャッシュを呼び出し、帯域幅モニターのサンプルで報告された帯域幅を観察します。
