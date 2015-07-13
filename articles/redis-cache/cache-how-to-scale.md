<properties 
	pageTitle="Azure Redis Cache のスケーリング方法" 
	description="Azure Redis Cache インスタンスをスケーリングする方法を学習する" 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache のスケーリング方法

>[AZURE.NOTE]Azure Redis Cache のスケーリング機能は現在プレビュー中です。

Azure Redis Cache は、キャッシュ サイズや機能の選択に柔軟性を持たせるために、さまざまなキャッシュ オファリングを用意しています。キャッシュを作成した後にご利用のアプリケーションの要件が変わった場合は、[Azure ポータル](https://portal.azure.com)の **[価格レベルの変更]** ブレードを使用して、キャッシュのサイズを変更することができます。

>[AZURE.NOTE]Azure Redis Cache をスケーリングする際にサイズは変更することができますが、Standard から Basic キャッシュ、またはその逆への変更はできません。

## スケーリングするタイミング

Azure Redis Cache の[監視](cache-how-to-monitor.md)機能を使用して、キャッシュ アプリケーションの正常性とパフォーマンスを監視し、キャッシュのスケーリングが必要であるかどうかを判断できます。

次のメトリックを監視して、スケーリングの必要性の判断に役立てることができます。

-	Redis サーバーの負荷
-	メモリ使用量
-	ネットワーク帯域幅
-	CPU 使用率

現在のキャッシュがアプリケーションの要件を満たさないと判断した場合は、ご利用のアプリケーションにとって適切な価格レベルのキャッシュに変更できます。使用するキャッシュの価格レベルを決定する方法の詳細については、「[Redis Cache のサービス内容と適切なサイズの選択](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」を参照してください。

## キャッシュのスケーリング
キャッシュをスケーリングするには、[Azure ポータル](https://portal.azure.com)で[キャッシュに移動](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration)して、**[Redis Cache]** ブレードで、**[Standard レベル]** または **[Basic レベル]** をクリックします。

![[価格レベル]][redis-cache-pricing-tier-part]

**[価格レベル]** ブレードから希望の価格レベルを選択し、**[選択]** をクリックします。

![[価格レベル]][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]キャッシュのプランを Basic から Standard、またはその逆に変更することはできません。また、250 MB のキャッシュにスケールダウンすることはできません。250 MB のキャッシュからより大きなサイズにスケールアップすることはできますが、250 BM の価格レベルに再度スケールダウンすることはできなくなります。Basic から Standard に変更する、または 250 MB のサイズにスケールダウンするには、新しいキャッシュを作成する必要があります。

キャッシュを新しい価格レベルにスケーリングする際に、**[Redis Cache]** ブレードで状態が **[拡大中]** と表示されます。

![スケーリング][redis-cache-scaling]

スケーリングが完了すると、状態が **[拡大中]** から **[実行中]** に変わります。

>[AZURE.IMPORTANT]スケーリング処理中、Basic キャッシュはオフラインになり、キャッシュ内のすべてのデータは失われます。スケーリング処理が完了すると、Basic キャッシュはデータが空の状態でオンラインに戻ります。Standard キャッシュはスケーリング処理中もオンラインのままで、一般的に Standard キャッシュをスケールアップする際にデータが失われることはありません。Standard キャッシュをスケールダウンする場合、新しいサイズがキャッシュ データ量より小さいとデータが失われる可能性があります。スケールダウンの際にデータが失われた場合、[allkeys-lru](http://redis.io/topics/lru-cache) 削除ポリシーを使用してキーが削除されます。Standard キャッシュには可用性について 99.9% の SLA がある一方で、データの喪失については SLA がありませんのでご注意ください。

## スケーリング処理を自動化する方法

Azure Redis Cache インスタンスは、Azure ポータルのほか、[Microsoft Azure 管理ライブラリ (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) を使用してスケーリングすることができます。キャッシュをスケーリングするには、`IRedisOperations.CreateOrUpdate` メソッドを呼び出して `RedisProperties.SKU.Capacity` に新しいサイズを指定します。

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/ja-jp/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

詳細については、[MAML を使用した Redis Cache の管理に関するページ](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)のサンプルを参照してください。

## スケーリングに関する FAQ

次の一覧は、Azure Redis Cache のスケーリングに関するよく寄せられる質問への回答です。

## スケーリング後にキャッシュ名やアクセス キーを変更する必要はありますか

いいえ、スケーリング処理中にキャッシュ名やキーが変更されることはありません。

## スケーリングはどのように処理されますか

**Basic** キャッシュをスケーリングすると、キャッシュはシャット ダウンされて新しいキャッシュが新しいサイズでプロビジョニングされます。この間キャッシュは使用できず、キャッシュ内のすべてのデータは失われます。

**Standard** キャッシュをスケーリングすると、一方のレプリカはシャット ダウンし、新しいサイズに再プロビジョニングされてデータが転送されます。他のレプリカは、キャッシュ ノードの 1 つでエラーが発生した際に発生する処理と同様に、再プロビジョニングされる前にフェールオーバーを実行します。

## スケーリング中にキャッシュからデータは失われますか

**Basic** キャッシュをスケーリングすると、すべてのデータが失われ、スケーリング処理中にキャッシュは使用できなくなります。

**Standard** キャッシュをより大きなサイズにスケールアップする場合、通常はすべてのデータは保持されます。**Standard** キャッシュをより小さいサイズにスケールダウンする場合、キャッシュ内のデータ量によってはスケーリング後にデータが失われる可能性があります。スケールダウンの際にデータが失われた場合、[allkeys-lru](http://redis.io/topics/lru-cache) 削除ポリシーを使用してキーが削除されます。Standard キャッシュには可用性について 99.9% の SLA がある一方で、データの喪失については SLA がありませんのでご注意ください。

## スケーリング中にキャッシュを使用できますか

**Standard** キャッシュはスケーリング処理中でも使用できます。

**Basic** キャッシュはスケーリング処理中はオフラインになります。

## サポートされていない処理

スケーリング時に **Basic** から **Standard** キャッシュ、またはその逆に変更することはできません。

**C0** (250 MB) キャッシュからより大きなサイズにスケールアップすることはできますが、**C0** キャッシュをより小さいサイズにスケールダウンすることはできません。

スケーリング処理でエラーが発生すると、サービスは処理を取り消してキャッシュを元のサイズに戻すように試行します。

## スケーリングにはどのくらいの時間がかかりますか

スケーリングには約 20 分かかりますが、キャッシュ内のデータ量によって変わります。

## スケーリングが完了したことをどのようにして確認できますか

スケーリング処理の進捗はポータルで確認できます。スケーリングが完了すると、キャッシュの状態が **[実行中]** に変わります。

## この機能をプレビューする理由は何ですか

私たちはフィードバックを取得する目的でこの機能をリリースしています。フィードバックに基づき、まもなくこの機能の一般提供を開始する予定です。





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO1-->