---
title: Azure Cache for Redis インスタンスのスケーリング
description: Azure portal、Azure PowerShell などのツール、Azure CLI を使用して Azure Cache for Redis インスタンスをスケーリングする方法を説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 5c8bf3839c7bae9b1c93b201bb52a4e7be371904
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538158"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスのスケーリング

Azure Cache for Redis には、キャッシュ サイズや機能の選択に柔軟性を持たせるために、さまざまなキャッシュ オファリングが用意されています。 Basic、Standard、または Premium のキャッシュについては、アプリケーションのニーズに合わせて、作成後にサイズやレベルを変更できます。 この記事では、Azure Portal と、Azure PowerShell や Azure CLI などのツールを使用して、キャッシュをスケーリングする方法を説明します。

## <a name="when-to-scale"></a>スケーリングするタイミング

Azure Cache for Redis の [監視](cache-how-to-monitor.md)機能を使用して、お使いのキャッシュの正常性とパフォーマンスを監視できます。 その情報を使用して、キャッシュのスケーリングが必要であるかどうかを判断します。

次のメトリックを監視して、スケーリングの必要性の判断に役立てることができます。

- Redis サーバーの負荷
  - Redis サーバーは、単一スレッドのプロセスです。 Redis サーバーの負荷が高いということは、サーバーがすべてのクライアント接続からの要求に対応できないという意味です。 このような状況では、クラスタリングを有効にしたり、シャード数を増やしたりして、オーバーヘッドの高い機能を複数の Redis プロセスに分散させることが役立ちます。 クラスタリングおよびシャード数の増加によって TLS の暗号化と暗号化解除が分散され、TLS 接続と切断が分散されます。
  - 詳細については、「[クラスタリングを設定する](cache-how-to-premium-clustering.md#set-up-clustering)」を参照してください。
- メモリ使用量
  - メモリ使用量が多い場合は、データ サイズが現在のキャッシュ サイズに対して大きすぎるかことを示します。 メモリがより大きいキャッシュ サイズにスケーリングを検討してください。
- クライアント接続
  - 各キャッシュ サイズには、サポートできるクライアント接続の数に制限があります。 クライアント接続がキャッシュ サイズの制限に近い場合は、より大きなレベルにスケールアップするか、スケールアウトしてクラスタリングを有効にし、シャード数を増やすことを検討してください。 選択は、Redis サーバーの負荷とメモリ使用量によって異なります。
  - キャッシュ サイズ別の接続制限の詳細については、「[Azure Cache for Redis 計画に関するよくあるご質問](./cache-planning-faq.yml)」を参照してください。
- ネットワーク帯域幅
  - Redis サーバーが使用可能な帯域幅を超えると、サーバーがクライアントに十分な速度でデータをプッシュできないので、クライアントの要求がタイムアウトする可能性があります。 "キャッシュの読み取り" および "キャッシュの書き込み" メトリックを使用して、サーバー側の帯域幅がどれだけ使用されているかを確認します。 Redis サーバーが使用可能なネットワーク帯域幅を超える場合は、より大きなネットワーク帯域幅を持つより大きなキャッシュ サイズにスケールアップを検討する必要があります。
  - キャッシュ サイズ別のネットワークで使用可能な帯域幅の詳細については、「[Azure Cache for Redis 計画に関するよくあるご質問](./cache-planning-faq.yml)」を参照してください。

キャッシュがアプリケーションの要件を満たさなくなったと判断した場合は、ご利用のアプリケーションにとって適切な価格レベルのキャッシュにスケーリングできます。 ニーズに合わせて、より大きいキャッシュまたは小さいキャッシュを選択できます。

使用するキャッシュの価格レベルを決定する方法の詳細については、「[最適なサービス レベルを選択する](cache-overview.md#choosing-the-right-tier)」および「[Azure Cache for Redis 計画に関するよくあるご質問](./cache-planning-faq.yml)」を参照してください。

## <a name="scale-a-cache"></a>キャッシュのスケーリング

キャッシュをスケーリングするには、[Azure portal](https://portal.azure.com) で [キャッシュを参照](cache-configure.md#configure-azure-cache-for-redis-settings)し、左側にある **[スケール]** を選択します。

:::image type="content" source="media/cache-how-to-scale/scale-a-cache.png" alt-text="リソース メニューの [スケール]":::

右側にある価格レベルを選択し、 **[選択]** を選択します。

:::image type="content" source="media/cache-how-to-scale/select-a-tier.png" alt-text="Azure Cache for Redis 階層":::

別の価格レベルにスケーリングできますが、次のような制約があります。

- 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
  - **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
  - **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
- **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後からスケーリング操作で必要なサイズに変更できます。
- **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、次の操作で **Standard** から **Premium** にスケーリングします。
- **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。 ただし、同じ価格帯の他のサイズにスケールダウンすることはできます。 たとえば、C5 Standard から C1 Standard にスケールダウンできます。

キャッシュを新しいレベルにスケーリングしている間、 **[Redis Cache のスケールを設定しています]** という通知が表示されます。

:::image type="content" source="media/cache-how-to-scale/scaling-notification.png" alt-text="スケーリングの通知":::

スケーリングが完了すると、状態が **[拡大中]** から **[実行中]** に変わります。

## <a name="how-to-automate-a-scaling-operation"></a>スケーリング処理を自動化する方法

キャッシュ インスタンスは、Azure portal で次の方法でスケーリングできます。 また、PowerShell コマンドレット、Azure CLI、および Microsoft Azure Management Libraries (MAML) を使用してスケーリングすることもできます。

- [PowerShell を使用したスケーリング](#scale-using-powershell)
- [Azure CLI を使用したスケーリング](#scale-using-azure-cli)
- [MAML を使用したスケーリング](#scale-using-maml)

### <a name="scale-using-powershell"></a>PowerShell を使用したスケーリング

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して Azure Cache for Redis インスタンスをスケーリングするには、`Size`、`Sku`、または `ShardCount` プロパティを変更するときに [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) コマンドレットを使用します。 次の例は、`myCache` という名前のキャッシュを 2.5 GB のキャッシュにスケーリングする方法を示しています。

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

PowerShell によるスケーリングの詳細については、[PowerShell を使用した Azure Cache for Redis のスケーリング](cache-how-to-manage-redis-cache-powershell.md#scale)に関するページを参照してください。

### <a name="scale-using-azure-cli"></a>Azure CLI を使用したスケーリング

Azure CLI を使用して Azure Cache for Redis インスタンスをスケーリングするには、`azure rediscache set` コマンドを呼び出し、必要なスケーリング操作に基づいて、新しいサイズ、sku、またはクラスター サイズを含む必要な構成変更を指定します。

Azure CLI によるスケーリングの詳細については、「[Change settings of an existing Azure Cache for Redis](cache-manage-cli.md#scale)」 (既存の Azure Cache for Redis の設定を変更する) をご覧ください。

### <a name="scale-using-maml"></a>MAML を使用したスケーリング

[Microsoft Azure 管理ライブラリ (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) を使用して Azure Cache for Redis インスタンスをスケーリングするには、`IRedisOperations.CreateOrUpdate` メソッドを呼び出して `RedisProperties.SKU.Capacity` に新しいサイズを指定します。

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
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
```

詳細については、[MAML を使用した Azure Cache for Redis の管理に関するページ](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)のサンプルをご覧ください。

## <a name="scaling-faq"></a>スケーリングに関する FAQ

次の一覧は、Azure Cache for Redis のスケーリングに関するよく寄せられる質問への回答です。

- [Premium キャッシュへのスケーリング、Premium キャッシュからのスケーリング、または Premium キャッシュ内でのスケーリングは可能ですか](#can-i-scale-to-from-or-within-a-premium-cache)
- [スケーリング後にキャッシュ名やアクセス キーを変更する必要はありますか](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [スケーリングはどのように処理されますか](#how-does-scaling-work)
- [スケーリング中にキャッシュからデータは失われますか](#will-i-lose-data-from-my-cache-during-scaling)
- [スケーリング中に影響を受けるカスタム データベース](#is-my-custom-databases-setting-affected-during-scaling)
- [スケーリング中にキャッシュを使用できますか](#will-my-cache-be-available-during-scaling)
- [geo レプリケーションにスケーリング制限はありますか](#are-there-scaling-limitations-with-geo-replication)
- [サポートされていない操作](#operations-that-arent-supported)
- [スケーリングにはどのくらいの時間がかかりますか](#how-long-does-scaling-take)
- [スケーリングが完了したことをどのようにして確認できますか](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Premium キャッシュへのスケーリング、Premium キャッシュからのスケーリング、または Premium キャッシュ内でのスケーリングは可能ですか

- **Premium** キャッシュから **Basic** または **Standard** の価格レベルにスケーリングすることはできません。
- ある **Premium** キャッシュの価格レベルから別の Premium キャッシュの価格レベルにスケーリングすることはできます。
- **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、次の操作で **Standard** から **Premium** にスケーリングします。
- **Premium** キャッシュを作成しているときにクラスタリングを有効にした場合、 [クラスター サイズを変更](cache-how-to-premium-clustering.md#cluster-size)できます。 クラスタリングを有効にせずに作成されたキャッシュの場合、後でクラスタリングを構成できます。

詳細については、「[How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md)」 (Premium Azure Cache for Redis のクラスタリングの構成方法) を参照してください。

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>スケーリング後にキャッシュ名やアクセス キーを変更する必要はありますか

いいえ、スケーリング処理中にキャッシュ名やキーが変更されることはありません。

### <a name="how-does-scaling-work"></a>スケーリングはどのように処理されますか

- **Basic** キャッシュを別のサイズにスケーリングすると、キャッシュはシャット ダウンされて新しいキャッシュが新しいサイズでプロビジョニングされます。 この間キャッシュは使用できず、キャッシュ内のすべてのデータは失われます。
- **Basic** キャッシュを **Standard** キャッシュにスケーリングすると、レプリカ キャッシュがプロビジョニングされ、データがプライマリ キャッシュからレプリカ キャッシュにコピーされます。 スケーリングの処理中、キャッシュは引き続き使用できます。
- **Standard** キャッシュを別のサイズまたは **Premium** キャッシュにスケーリングすると、一方のレプリカはシャットダウンし、新しいサイズに再プロビジョニングされてデータが転送されます。もう一方のレプリカは、キャッシュ ノードの 1 つでエラーが発生したときに実行される処理と同様に、フェールオーバーを実行してから、再プロビジョニングされます。
- クラスター化されたキャッシュをスケールアウトすると、新しいシャードがプロビジョニングされ、Redis サーバー クラスターに追加されます。 その後、すべてのシャード間でデータが再シャードされます。
- クラスター化キャッシュでスケーリングすると、最初にデータが再調整され、クラスターサイズが必要なシャードに縮小されます。

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>スケーリング中にキャッシュからデータは失われますか

- 新しいサイズに **Basic** キャッシュをスケーリングすると、すべてのデータが失われ、スケーリング処理中にキャッシュは使用できなくなります。
- **Basic** キャッシュを **Standard** キャッシュにスケーリングする場合、通常、キャッシュのデータは保存されます。
- **Standard** キャッシュをより大きいサイズまたは価格レベルにスケールアップする場合や、**Premium** キャッシュをより大きいサイズにスケールアップする場合、通常はすべてのデータが保持されます。 Standard または Premium のキャッシュを小さいサイズにスケーリングする場合、データサイズが縮小されたときに新しいサイズを超えるとデータが失われる可能性があります。 スケールダウンのときにデータが失われた場合、 [allkeys-lru](https://redis.io/topics/lru-cache) 削除ポリシーを使用してキーが削除されます。

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>スケーリング中に影響を受けるカスタム データベース

キャッシュの作成中に `databases` の設定のカスタム値を構成した場合、価格レベルによってさまざまな[データベース制限](cache-configure.md#databases)があることに注意してください。 このシナリオでスケーリングを行う場合は、次の点を考慮します。

- 現在のレベルより低い `databases` の制限で価格レベルをスケーリングする場合:
  - すべての価格レベルが 16 の `databases` の既定の数を使っている場合、データは失われません。
  - スケーリングしているレベルの制限範囲に含まれるユーザー設定の数値の `databases` を使用している場合、この `databases` の設定は保持され、データは失われません。
  - 新しいレベルの制限を超えるユーザー設定の数値の `databases` を使用している場合、`databases` の設定は新しいレベルの制限より低くなり、削除されたデータベースのすべてのデータが失われます。
- 現在のレベル以上の `databases` の制限を持つ価格レベルにスケーリングする場合、`databases` の設定は保持され、データは失われません。

Standard および Premium キャッシュには可用性について 99.9% の SLA がある一方で、データの喪失については SLA がありません。

### <a name="will-my-cache-be-available-during-scaling"></a>スケーリング中にキャッシュを使用できますか

- **Standard** キャッシュと **Premium** キャッシュはスケーリング処理中でも使用できます。 ただし、Standard および Premium キャッシュのスケーリングや、Basic から Standard キャッシュへのスケーリングの際に接続の中断が発生する可能性があります。 こうした接続の中断はわずかで、Redis クライアントは通常すぐに接続を再確立できます。
- 別のサイズにスケーリングする場合、**Basic** キャッシュはオフラインになります。 **Basic** から **Standard** にスケーリングする際は、Basic キャッシュを引き続き利用できますが、接続の中断がわずかに発生する可能性があります。 接続の中断が発生しても、Redis クライアントは通常、すぐに接続を再確立できます。

### <a name="are-there-scaling-limitations-with-geo-replication"></a>geo レプリケーションにスケーリング制限はありますか

geo レプリケーションが構成されている場合は、キャッシュをスケーリングしたり、クラスター内のシャードを変更したりすることはできません。 2 つのキャッシュ間の geo レプリケーション リンクを使用すると、クラスター内のスケーリング操作やシャードの数の変更を防ぐことができます。 これらのコマンドを発行するには、キャッシュのリンクを解除する必要があります。 詳細については、「[geo レプリケーションの構成](cache-how-to-geo-replication.md)」を参照してください。

### <a name="operations-that-arent-supported"></a>サポートされていない操作

- 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
  - **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
  - **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
- **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後からスケーリング操作で必要なサイズに変更できます。
- **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、次の操作で **Standard** から **Premium** にスケーリングします。
- **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。

スケール処理でエラーが発生すると、サービスは処理を取り消してキャッシュを元のサイズに戻すように試行します。

### <a name="how-long-does-scaling-take"></a>スケーリングにはどのくらいの時間がかかりますか

スケーリング時間はいくつかの要因によって異なります。 ここでは、スケーリングにかかる時間に影響する要因について説明します。

- データ量: 大量のデータがレプリケートされるまでに長い時間がかかります。
- 高書き込み要求: 書き込み数が多いほど、ノードまたはシャード間でデータがレプリケートされることを意味します。
- サーバーが高負荷: サーバーの負荷が高い場合、Redis サーバーがビジー状態になり、データの再配布を完了するための CPU サイクルが制限されます。

一般に、データを含まないキャッシュをスケーリングする場合は、約 20 分かかります。 クラスター化されたキャッシュの場合、スケーリングには最小データでシャードあたり約 20 分かかります。

### <a name="how-can-i-tell-when-scaling-is-complete"></a>スケーリングが完了したことをどのようにして確認できますか

スケール処理の進捗は Azure Portal で確認できます。 スケーリングが完了すると、キャッシュの状態が **[実行中]** に変わります。
