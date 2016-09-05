<properties 
	pageTitle="Azure Redis Cache の構成方法 | Microsoft Azure"
	description="Azure Redis Cache の既定の Redis 構成を理解し、Azure Redis Cache インスタンスの構成方法について説明します。"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/18/2016"
	ms.author="sdanie" />

# Azure Redis Cache の構成方法

このトピックでは、Azure Redis Cache インスタンスの構成を確認し、更新する方法と、Azure Redis Cache インスタンス用の既定の Redis サーバー構成について説明します。

>[AZURE.NOTE] Premium Cache 機能の構成と使用について詳しくは、「[Premium Azure Redis Cache のデータ永続化の構成方法](cache-how-to-premium-persistence.md)」、「[Premium Azure Redis Cache の Redis クラスタリングの構成方法](cache-how-to-premium-clustering.md)」、および「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」をご覧ください。

## Redis Cache の設定の構成

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis Cache の **[設定]** ブレードには次の設定が用意されています。

![Redis Cache の設定](./media/cache-configure/redis-cache-settings.png)



-	[サポートおよびトラブルシューティング設定](#support-amp-troubleshooting-settings)
-	[全般設定](#general-settings)
	-	[プロパティ](#properties)
	-	[アクセス キー](#access-keys)
	-	[詳細設定](#advanced-settings)
	-	[Redis Cache Advisor](#redis-cache-advisor)
-	[スケールの設定](#scale-settings)
	-	[[価格レベル]](#pricing-tier)
	-	[Redis クラスター サイズ](#cluster-size)
-	[データ管理設定](#data-management-settings)
	-	[Redis データの保持](#redis-data-persistence)
	-	[Import/Export](#importexport)
-	[管理の設定](#administration-settings)
	-	[Reboot](#reboot)
	-	[更新のスケジュール](#schedule-updates)
-	[診断設定](#diagnostics-settings)
-	[ネットワーク設定](#network-settings)
-	[リソース管理設定](#resource-management-settings)

## サポートおよびトラブルシューティング設定

**[サポート + トラブルシューティング]** セクションでは、キャッシュの問題を解決するためのオプションを設定できます。

![サポートとトラブルシューティング](./media/cache-configure/redis-cache-support-troubleshooting.png)

一般的な問題と、その問題を解決するため戦略を確認するには、**[トラブルシューティング]** をクリックします。

キャッシュに対して実行された操作を表示するには、**[監査ログ]** をクリックします。また、フィルター処理を使用すれば、ビューを拡張してその他のリソースを含めることができます。監査ログの操作方法の詳細については、「[イベントと監査ログの表示](../azure-portal/insights-debugging-with-events.md)」と「[Resource Manager の監査操作](../resource-group-audit.md)」を参照してください。Azure Redis Cache イベントの監視の詳細については、「[処理とアラート](cache-how-to-monitor.md#operations-and-alerts)」を参照してください。

**[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。Azure Resource Health サービスの詳細については、「[Azure Resource Health の概要](../resource-health/resource-health-overview.md)」を参照してください。

>[AZURE.NOTE] 現在、[リソース正常性] では、仮想ネットワークでホストされている Azure Redis Cache インスタンスの正常性については報告できません。詳細については、「[VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)」をご覧ください。

**[新しいサポート要求]** をクリックして、キャッシュのサポート要求を開きます。

## 全般設定

**[全般]** セクションの設定では、キャッシュに関する次の設定にアクセスして構成できます。

![全般設定](./media/cache-configure/redis-cache-general-settings.png)

-	[プロパティ](#properties)
-	[アクセス キー](#access-keys)
-	[詳細設定](#advanced-settings)
-	[Redis Cache Advisor](#redis-cache-advisor)

### プロパティ

**[プロパティ]** をクリックすると、キャッシュ エンドポイントやポートなど、キャッシュに関する情報を表示できます。

![Redis Cache のプロパティ](./media/cache-configure/redis-cache-properties.png)

### アクセス キー

**[アクセス キー]** をクリックすると、キャッシュのアクセス キーを表示したり、再生成したりできます。これらのキーは、キャッシュに接続するクライアントによって、**[プロパティ]** ブレードのホスト名とポートと共に使用されます。

![Redis Cache のアクセス キー](./media/cache-configure/redis-cache-manage-keys.png)






### 詳細設定

次の設定は、**[詳細設定]** ブレードで構成します。

-	[アクセス ポート](#access-ports)
-	[maxmemory-policy と maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)
-	[キースペース通知 (詳細設定)](#keyspace-notifications-advanced-settings)


### アクセス ポート

新しいキャッシュでは、SSL を使用しないアクセスが既定で無効になっています。非 SSL ポートを有効にするには、**[詳細設定]** ブレードにある **[SSL によるアクセスのみ許可する]** で **[いいえ]** をクリックしてから、**[保存]** をクリックします。

![Redis Cache のアクセス ポート](./media/cache-configure/redis-cache-access-ports.png)

### maxmemory-policy と maxmemory-reserved

**[詳細設定]** ブレードの **[Maxmemory ポリシー]** と **[xmemory-reserved]** の設定によって、キャッシュのメモリ ポリシーが構成されます。**maxmemory-policy** 設定は、キャッシュの削除ポリシーを構成し、**maxmemory-reserved** は、キャッシュ以外のプロセスのために予約されたメモリを構成します。

![Redis Cache の Maxmemory ポリシー](./media/cache-configure/redis-cache-maxmemory-policy.png)

**[Maxmemory ポリシー]** では、次の削除ポリシーから選択できます。

-	volatile-lru - これが、既定値です。
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Maxmemory ポリシーの詳細については、[削除ポリシー](http://redis.io/topics/lru-cache#eviction-policies)に関するトピックを参照してください。

**maxmemory-reserved** 設定は、フェールオーバーに伴うレプリケーションなどのキャッシュ以外の操作のために予約されているメモリの量を、MB 単位で構成するものです。また、断片化率が高い場合も使用できます。この値を設定すると、負荷が変化するときでも、Redis サーバーの稼働状態がより安定します。この値は、書き込みが大量に発生するワークロードに対しては、高く設定する必要があります。メモリがこのような操作のために予約されていると、キャッシュされたデータの保存に使用できなくなります。

>[AZURE.IMPORTANT] **maxmemory-reserved** 設定は、Standard キャッシュと Premium キャッシュにのみ使用可能です。

### キースペース通知 (詳細設定)

Redis キースペース通知は、**[詳細設定]** ブレードで構成します。キースペース通知により、特定のイベントが発生したときに、クライアントが通知を受信できます。

![Redis Cache の高度な設定](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] キースペース通知と **notify-keyspace-events** 設定は、Standard キャッシュと Premium キャッシュに対してのみ使用可能です。

詳細については、[Redis キースペース通知](http://redis.io/topics/notifications)に関するトピックを参照してください。サンプル コードについては、[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルの [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) ファイルを参照してください。

<a name="recommendations"></a>
## Redis Cache Advisor

**[推奨事項]** ブレードにキャッシュに関する推奨事項が表示されます。通常の操作中に推奨事項は表示されません。

![推奨事項](./media/cache-configure/redis-cache-no-recommendations.png)

キャッシュの操作中に、高いメモリ使用量、ネットワーク帯域幅、サーバー負荷などの状況が発生した場合は、**[Redis Cache]** ブレードにアラートが表示されます。

![推奨事項](./media/cache-configure/redis-cache-recommendations-alert.png)

詳細情報は、**[推奨事項]** ブレードで確認できます。

![推奨事項](./media/cache-configure/redis-cache-recommendations.png)

**[Redis Cache]** ブレードの [[Monitoring charts (監視グラフ)]](cache-how-to-monitor.md#monitoring-charts) および [[Usage charts (使用状況グラフ)]](cache-how-to-monitor.md#usage-charts) セクションでは、以下のメトリックを監視できます。

各価格レベルには、クライアント接続、メモリ、および帯域幅についてさまざまな制限があります。長時間にわたり、キャッシュがこれらのメトリックの最大容量に近づいている場合は、推奨項目が作成されます。**[推奨事項]** ツールで検証されるメトリックと制限の詳細については、次の表をご覧ください。

| Redis Cache メトリック | 詳細情報の参照先 |
|-------------------------|---------------------------------------------------------------------------|
| ネットワーク帯域幅の使用量 | [キャッシュ パフォーマンス - 使用できる帯域幅](cache-faq.md#cache-performance) |
| 接続されているクライアント数 | [既定の Redis サーバー構成 - maxclients](#maxclients) |
| サーバーの負荷 | [使用状況グラフ - Redis サーバーの負荷](cache-how-to-monitor.md#usage-charts) |
| メモリ使用量 | [キャッシュのパフォーマンス - サイズ](cache-faq.md#cache-performance) |

キャッシュをアップグレードするには、**[今すぐアップグレード]** をクリックして、[価格レベル](#pricing-tier)を変更し、キャッシュのスケーリングを行います。価格レベルの選択の詳細については、「[Redis Cache のサービス内容と適切なサイズの選択](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」をご覧ください。

## スケールの設定

**[スケール]** セクションの設定では、キャッシュに関する次の設定にアクセスして構成できます。

![ネットワーク](./media/cache-configure/redis-cache-scale.png)

-	[[価格レベル]](#pricing-tier)
-	[Redis クラスター サイズ](#cluster-size)

### [価格レベル]

キャッシュの価格レベルを表示または変更するには、**[価格レベル]** をクリックします。スケーリングの詳細については、「[Azure Redis Cache のスケーリング方法](cache-how-to-scale.md)」をご覧ください。

![Redis Cache 価格レベル](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### Redis クラスター サイズ

クラスタリングが有効になっている実行中の Premium キャッシュのクラスター サイズを変更するには、**[(プレビュー) Redis クラスター サイズ]** をクリックします。

>[AZURE.NOTE] Azure Redis Cache の Premium レベルは一般公開されていますが、Redis クラスター サイズ機能は現在プレビュー段階であることに注意してください。

![Redis クラスター サイズ](./media/cache-configure/redis-cache-redis-cluster-size.png)

クラスター サイズを変更するには、スライダーを使用するか、**[シャード数]** ボックスに 1 ～ 10 の範囲の数値を入力し、**[OK]** をクリックして保存します。

>[AZURE.IMPORTANT] Redis クラスタリングは、Premium キャッシュでのみ使用できます。詳細については、「[Premium Azure Redis Cache のクラスタリングの構成方法](cache-how-to-premium-clustering.md)」を参照してください。


## データ管理設定

**[データ管理]** セクションの設定では、キャッシュに関する次の設定にアクセスして構成できます。

![データ管理](./media/cache-configure/redis-cache-data-management.png)

-	[Redis データの保持](#redis-data-persistence)
-	[Import/Export](#importexport)

### Redis データの保持

**[Redis データ永続化]** をクリックして、Premium Cache のデータ永続化を有効にする、無効にする、または構成することができます。

![Redis データの保持](./media/cache-configure/redis-cache-persistence-settings.png)

Redis の永続化を有効にするには、**[有効]** をクリックして RDB (Redis データベース) のバックアップを有効にします。Redis の永続化を無効にするには、**[無効]** をクリックします。

バックアップの間隔を構成するには、ドロップダウン リストから **[バックアップの頻度]** を選択します。選択肢は、**15 分**、**30 分**、**60 分**、**6 時間**、**12 時間**、**24 時間**です。前のバックアップ操作が正常に完了するとこの間隔のカウントダウンが開始し、期間が経過すると新しいバックアップが開始されます。

**[ストレージ アカウント]** をクリックして使用するストレージ アカウントを選択し、**[ストレージ キー]** ドロップダウンから使用する**プライマリ キー**または**セカンダリ キー**を選択します。Cache と同じリージョンのストレージ アカウントを選択する必要があり、また、スループットが高いため **Premium Storage** アカウントを使用することをお勧めします。永続化アカウントのストレージ キーを再生成する場合は常に、**[ストレージ キー]** ドロップダウンから目的のキーを再選択する必要があります。

**[OK]** をクリックして永続化の構成を保存します。

>[AZURE.IMPORTANT] Redis のデータ永続化は、Premium Cache でのみ使用できます。詳細については、「[Premium Azure Redis Cache の永続化の構成方法](cache-how-to-premium-persistence.md)」を参照してください。

### Import/Export

Import/Export は Azure Redis Cache のデータ管理操作です。Redis Cache データベース (RDB) のスナップショットを Premium キャッシュからエクスポートし、Azure ストレージ アカウント内のページ BLOB にインポートすることで、Azure Redis Cache との間でデータのインポートとエクスポートを実行できます。これにより、異なる Azure Redis Cache インスタンス間での移行または使用前のキャッシュへのデータ入力が可能になります。

インポートは、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。インポート処理中に、Azure Redis Cache は RDB ファイルを Azure ストレージからメモリに読み込み、キーをキャッシュに挿入します。

エクスポート機能では、Azure Redis Cache に格納されたデータを Redis と互換性のある RDB ファイルにエクスポートできます。この機能を使えば、Azure Redis Cache インスタンス間でデータを移動したり、Azure Redis Cache インスタンスから別の Redis サーバーにデータを移動したりできます。エクスポート処理中に、Azure Redis Cache サーバー インスタンスをホストする VM 上に一時ファイルが作成され、それが指定されたストレージ アカウントにアップロードされます。エクスポート操作が完了したら、操作の成否にかかわらず、この一時ファイルは削除されます。

>[AZURE.IMPORTANT] Import/Export は、Premium レベル キャッシュにのみ使用可能です。詳細および手順については、「[Azure Redis Cache でデータをインポートまたはエクスポートする](cache-how-to-import-export-data.md)」をご覧ください。


## 管理の設定

**[管理]** セクションの設定では、Premium キャッシュに対して次の管理タスクを実行できます。

![管理](./media/cache-configure/redis-cache-administration.png)

-	[Reboot](#reboot)
-	[更新のスケジュール](#schedule-updates)

>[AZURE.IMPORTANT] このセクションの設定は、Premium レベルのキャッシュにのみ使用できます。

### Reboot

**[再起動]** ブレードでは、キャッシュの 1 つ以上のノードを再起動できます。これにより、障害発生時のアプリケーションの回復性をテストすることができます。

![Reboot](./media/cache-configure/redis-cache-reboot.png)

クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するキャッシュのシャードを選択できます。

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

キャッシュのノードを再起動するには、目的のノードを選択し、**[再起動]** をクリックします。クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するシャードを選択し、**[再起動]** をクリックします。数分後、選択したノードが再起動され、さらに数分後にオンラインに戻ります。

>[AZURE.IMPORTANT] 再起動は、Premium レベルのキャッシュにのみ使用できます。詳細および手順については、[Azure Redis Cache の管理 - 再起動](cache-administration.md#reboot)に関するページをご覧ください。

### 更新のスケジュール

**[更新のスケジュール]** ブレードでは、キャッシュの Redis サーバー更新のメンテナンス時間を指定できます。

>[AZURE.IMPORTANT] このメンテナンス時間は、Redis サーバーの更新にのみ適用されることに気を付けてください。Azure の更新や、キャッシュをホストする VM のオペレーティング システムへの更新には適用されません。

![更新のスケジュール](./media/cache-configure/redis-schedule-updates.png)

メンテナンス時間を指定するには、目的の曜日をオンにし、曜日ごとにメンテナンス時間の開始時刻を指定して、**[OK]** をクリックします。メンテナンス時間の時刻は UTC 時間で指定します。

>[AZURE.IMPORTANT] 更新のスケジュールは、Premium レベルのキャッシュにのみ使用できます。詳細および手順については、[Azure Redis Cache の管理 - 更新のスケジュール](cache-administration.md#schedule-updates)に関するページをご覧ください。

## 診断設定

**[診断]** セクションでは、Redis Cache に対する診断を構成できます。

![診断](./media/cache-configure/redis-cache-diagnostics.png)

**[診断]** をクリックすると、キャッシュ診断の格納に使用する[ストレージ アカウントを構成](cache-how-to-monitor.md#enable-cache-diagnostics)できます。

![Redis Cache の診断](./media/cache-configure/redis-cache-diagnostics-settings.png)

**[Redis メトリックス]** をクリックして、キャッシュの[メトリックスを表示](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts)し、**[アラート ルール]** をクリックして、[アラート ルールを設定](cache-how-to-monitor.md#operations-and-alerts)します。

Azure Redis Cache 診断の詳細については、「[Azure Redis Cache の監視方法](cache-how-to-monitor.md)」をご覧ください。


## ネットワーク設定

**[ネットワーク]** セクションの設定では、キャッシュに関する次の設定にアクセスして構成できます。

![ネットワーク](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] 仮想ネットワークの設定は、キャッシュ作成時に VNET サポートで構成されたプレミアム キャッシュでのみ使用できます。VNET サポートでのプレミアム キャッシュの作成およびその設定の更新の詳細については、「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」をご覧ください。

## リソース管理設定

![リソース管理](./media/cache-configure/redis-cache-resource-management.png)

**[タグ]** セクションでは、リソースを整理できます。詳細については、[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)に関するページを参照してください。

**[ロック]** セクションでは、サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止できます。詳細については、「[Azure リソース マネージャーによるリソースのロック](../resource-group-lock-resources.md)」を参照してください。

**[ユーザー]** セクションでは、Azure ポータルでのロールベースのアクセス制御 (RBAC) をサポートしているため、組織はアクセス管理の要件を簡単かつ正確に満たすことができます。詳細については、[Azure ポータルでのロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)に関するページをご覧ください。

**[テンプレートのエクスポート]** をクリックして、将来のデプロイのために、デプロイ済みのリソースのテンプレートをビルドおよびエクスポートします。テンプレートを操作する方法の詳細については、[Azure Resource Manager のテンプレートを使用したリソースのデプロイ](../resource-group-template-deploy.md)に関するページをご覧ください。

## 既定の Redis サーバー構成

新しい Azure Redis Cache インスタンスに適用される既定の Redis 構成値は、次のとおりです。

>[AZURE.NOTE] `StackExchange.Redis.IServer.ConfigSet` メソッドを使用してこのセクションの設定を変更することはできません。このセクションのいずれかのコマンドでこのメソッドを呼び出すと、次のような例外がスローされます。
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>**max-memory-policy** などの構成可能な値を、Azure ポータルまたはコマンド ライン管理ツール (Azure CLI、PowerShell など) を使用して構成できます。

|Setting|既定値|Description|
|---|---|---|
|databases|16|データベースの既定の数は 16 ですが、価格レベルに基づいてさまざまな数を構成できます。<sup>1</sup> 既定のデータベースは DB 0 です。dbid が `0` ～ `databases - 1` の数値の `connection.GetDatabase(dbid)` を使用して、接続ごとに異なるデータベースを選択できます。|
|maxclients|価格レベルによって異なります。<sup>2</sup>|これは、同時に接続が許可されているクライアントの最大数です。制限に達すると、Redis はすべての新しい接続を終了し、エラー 'max number of clients reached' を送信します。|
|maxmemory-policy|volatile-lru|Maxmemory ポリシーは、maxmemory (キャッシュ作成時に選択したキャッシュのサイズ) に達したときに、Redis が削除する項目を選択する方法についての設定です。Azure Redis Cache の既定の設定は volatile-lru で、LRU アルゴリズムを使用して有効期限が設定されたキーを削除します。この設定は、Azure ポータルで構成できます。詳細については、「[maxmemory-policy と maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)」を参照してください。|
|maxmemory-samples|3|LRU アルゴリズムと最小 TTL アルゴリズムは精緻なアルゴリズムではなく、(メモリを節約するための) 近似アルゴリズムです。そのため、サンプル サイズも選択して確認できます。既定の Redis インスタンスの場合、キーを 3 つ確認し、直近の使用頻度が比較的低い ものを 1 つ選択します。|
|lua-time-limit|5,000|Lua スクリプトの最大実行時間 (ミリ秒)。最大実行時間に達した場合は、Redis は、最大許容時間の後もスクリプトが実行中であることをログに記録し、クエリに対してエラーを知らせる応答を開始します。|
|lua-event-limit|500|これは、スクリプト イベント キューの最大サイズです。|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|このクライアントの出力バッファー制限を使用して、なんらかの理由 (一般的には、パブリッシャーがメッセージを作成するのと同じ速度で Pub/Sub クライアントがメッセージを利用できないという理由) により、サーバーから十分な速度でデータを読み込んでいないクライアントを強制的に切断することができます。詳細については、[http://redis.io/topics/clients](http://redis.io/topics/clients) を参照してください。|

<a name="databases"></a> <sup>1</sup>`databases` の制限は、Azure Redis Cache の価格レベルによって異なり、キャッシュの作成時に設定できます。キャッシュの作成中に `databases` を設定しない場合は、既定値の 16 が使用されます。

-	Basic キャッシュおよび Standard キャッシュ
	-	C0 (250 MB) キャッシュ - 最大 16 のデータベース
	-	C1 (1 GB) キャッシュ - 最大 16 のデータベース
	-	C2 (2.5 GB) キャッシュ - 最大 16 のデータベース
	-	C3 (6 GB) キャッシュ - 最大 16 のデータベース
	-	C4 (13 GB) キャッシュ - 最大 32 のデータベース
	-	C5 (26 GB) キャッシュ - 最大 48 のデータベース
	-	C6 (53 GB) キャッシュ - 最大 64 のデータベース
-	Premium キャッシュ
	-	P1 (6 GB ～ 60 GB) - 最大 16 のデータベース
	-	P2 (13 GB ～ 130 GB) - 最大 32 のデータベース
	-	P3 (26 GB ～ 260 GB) - 最大 48 のデータベース
	-	P4 (53 GB ～ 530 GB) - 最大 64 のデータベース
	-   Redis クラスターが有効なすべての Premium キャッシュ - Redis クラスターは、データベース 0 の使用のみをサポートするため、Redis クラスターが有効な Premium キャッシュの `databases` の制限は、実質的に 1 で、[Select](http://redis.io/commands/select) コマンドは使用できません。詳細については、「[クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)」をご覧ください。


>[AZURE.NOTE] `databases` の設定は、キャッシュの作成中にのみ構成できます。また、PowerShell、CLI、その他の管理クライアントを使用する必要があります。PowerShell を使用して、キャッシュの作成中に `databases` を構成する例については、[New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases) に関するページをご覧ください。


<a name="maxclients"></a> <sup>2</sup>`maxclients` は、Azure Redis Cache の価格レベルによって異なります。

-	Basic キャッシュおよび Standard キャッシュ
	-	C0 (250 MB) キャッシュ - 最大 256 接続
	-	C1 (1 GB) キャッシュ - 最大 1,000 接続
	-	C2 (2.5 GB) キャッシュ - 最大 2,000 接続
	-	C3 (6 GB) キャッシュ - 最大 5,000 接続
	-	C4 (13 GB) キャッシュ - 最大 10,000 接続
	-	C5 (26 GB) キャッシュ - 最大 15,000 接続
	-	C6 (53 GB) キャッシュ - 最大 20,000 接続
-	Premium キャッシュ
	-	P1 (6 ～ 60 GB) - 最大 7,500 接続
	-	P2 (13 ～ 130 GB) - 最大 15,000 接続
	-	P3 (26 ～ 260 GB) - 最大 30,000 接続
	-	P4 (53 ～ 530 GB) - 最大 40,000 接続

## Azure Redis Cache でサポートされない Redis コマンド

>[AZURE.IMPORTANT] Azure Redis Cache インスタンスの構成と管理はマイクロソフトによって管理されるため、次のコマンドは無効です。これらのコマンドを呼び出そうとすると、`"(error) ERR unknown command"` のようなエラー メッセージを受け取ります。
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	保存
>-	SHUTDOWN
>-	SLAVEOF
>-	CLUSTER - クラスターの書き込みコマンドは無効ですが、読み取り専用のクラスター コマンドは許可されます。

Redis コマンドの詳細については、[http://redis.io/commands](http://redis.io/commands) を参照してください。

## Redis コンソール

**Redis コンソール**を使用して Azure Redis Cache インスタンスにコマンドを安全に発行できます。このコンソールは Standard キャッシュと Premium キャッシュに対して使用できます。

>[AZURE.IMPORTANT] Redis コンソールは、VNET、クラスタリング、0 以外のデータベースでは機能しません。
>
>-	[VNET](cache-how-to-premium-vnet.md): キャッシュが VNET の一部である場合は、VNET のクライアントだけがキャッシュにアクセスできます。Redis コンソールは、VNET の一部ではない VM でホストされている redis-cli.exe クライアントを使用するため、キャッシュに接続できません。
>-	[クラスタリング](cache-how-to-premium-clustering.md): Redis コンソールは、現時点ではクラスタリングをサポートしていない redis-cli.exe クライアントを使用します。GitHub で Redis リポジトリの[不安定な](http://redis.io/download)ブランチにある redis-cli ユーティリティは、`-c` スイッチ付きで起動した場合、基本的なサポートを実装しています。詳細については、[http://redis.io](http://redis.io) の [Redis クラスター チュートリアル](http://redis.io/topics/cluster-tutorial)で「[クラスターの使用](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」を参照してください。
>-	Redis コンソールは、コマンドを送信するたびに、データベース 0 への新しい接続を作成します。データベースは各コマンドで 0 にリセットされるため、`SELECT` コマンドを使用して別のデータベースを選択することはできません。別のデータベースへの変更など、Redis コマンドの実行については、「[Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)」をご覧ください。

Redis コンソールにアクセスするには、**[Redis Cache]** ブレードの **[コンソール]** をクリックします。

![Redis コンソール](./media/cache-configure/redis-console-menu.png)

キャッシュ インスタンスに対してコマンドを発行するには、目的のコマンドをコンソールに入力します。

![Redis コンソール](./media/cache-configure/redis-console.png)

Azure Redis Cache で無効な Redis コマンドの一覧については、前の「[Azure Redis Cache でサポートされない Redis コマンド](#redis-commands-not-supported-in-azure-redis-cache)」セクションを参照してください。Redis コマンドの詳細については、[http://redis.io/commands](http://redis.io/commands) を参照してください。

## 新しいサブスクリプションへのキャッシュの移動

新しいサブスクリプションにキャッシュを移動するには、**[移動]** をクリックします。

![Redis Cache の移動](./media/cache-configure/redis-cache-move.png)

リソース グループ間、およびサブスクリプション間でのリソースの移動については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」をご覧ください。

## 次のステップ
-	Redis コマンドの使用の詳細については、[Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)に関するページを参照してください。

<!---HONumber=AcomDC_0824_2016-->