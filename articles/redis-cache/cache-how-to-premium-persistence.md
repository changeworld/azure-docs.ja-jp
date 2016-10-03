<properties 
	pageTitle="Premium Azure Redis Cache のデータ永続化の構成方法" 
	description="Premium レベルの Azure Redis Cache インスタンス用にデータの永続化を構成して管理する方法について説明します" 
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
	ms.date="09/15/2016" 
	ms.author="sdanie"/>

# Premium Azure Redis Cache のデータ永続化の構成方法

Azure Redis Cache には、新しい Premium レベルなど、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。

Azure Redis Cache の Premium レベルには、クラスタリング、永続化、仮想ネットワークのサポートなどの機能が含まれています。この記事では、Premium Azure Redis Cache インスタンスで永続化を構成する方法について説明します。

Premium キャッシュのその他の機能の詳細については、「[Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)」を参照してください。

## データの永続化とは
Redis の永続化を使用すると、Redis に格納されたデータを保持できます。また、スナップショットを取得したりデータをバックアップしたりして、ハードウェア障害のときに読み込むことができます。これは、Basic レベルや Standard レベルにはない大きな利点です。Basic/Standard レベルでは、すべてのデータはメモリに格納され、Cache ノードがダウンするような障害時にはデータが失われる可能性があります。

Azure Redis Cache の Redis 永続化では、[RDB モデル](http://redis.io/topics/persistence)を使用してデータを Azure ストレージ アカウントに格納します。永続化を構成すると、Azure Redis Cache は、Redis Cache のスナップショットを構成可能なバックアップ頻度に基づいて Redis バイナリ形式でディスクに保存します。プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュが再構築されます。

永続化は、キャッシュの作成中に **[新規 Redis Cache]** ブレードから、また既存の Premium キャッシュ用の **[設定]** ブレードで構成できます。

## Premium キャッシュの作成

キャッシュを作成し、永続化を構成するには、[Azure ポータル](https://portal.azure.com)にサインインし、**[新規]**、**[データ + ストレージ]**、**[Redis Cache]** の順にクリックします。

![Redis Cache の作成][redis-cache-new-cache-menu]

永続化を構成するには、まず **[料金レベルの選択]** ブレードで **[Premium]** キャッシュのいずれかを選択します。

![価格レベルの選択][redis-cache-premium-pricing-tier]

Premium 価格レベルを選択した後、**[Redis の永続化]** をクリックします。

![Redis の永続化][redis-cache-persistence]

次のセクションの手順は、新しい Premium キャッシュに Redis の永続化を構成する方法を示します。Redis の永続化が構成されたら、**[作成]** をクリックして、Redis の永続化で新規 Premium キャッシュを作成します。

## Redis の永続化を構成する

Redis の永続化は、**[Redis データ永続化]** ブレードで構成します。新規キャッシュでは、前のセクションで説明したように、このブレードにはキャッシュの作成プロセス中にアクセスします。既存のキャッシュでは、**[Redis データ永続化]** ブレードには、キャッシュの **[設定]** ブレードからアクセスします。

![Redis の設定][redis-cache-settings]

Redis の永続化を有効にするには、**[有効]** をクリックして RDB (Redis データベース) のバックアップを有効にします。以前に有効にされた Premium キャッシュの Redis 永続化を無効にするには、**[無効]** をクリックします。

バックアップの間隔を構成するには、ドロップダウン リストから **[バックアップの頻度]** を選択します。選択肢は、**15 分**、**30 分**、**60 分**、**6 時間**、**12 時間**、**24 時間**です。前のバックアップ操作が正常に完了するとこの間隔のカウントダウンが開始し、期間が経過すると新しいバックアップが開始されます。

**[ストレージ アカウント]** をクリックして使用するストレージ アカウントを選択し、**[ストレージ キー]** ドロップダウンから使用する**プライマリ キー**または**セカンダリ キー**を選択します。Cache と同じリージョンのストレージ アカウントを選択する必要があり、また、スループットが高いため **Premium Storage** アカウントを使用することをお勧めします。

>[AZURE.IMPORTANT] 永続化アカウントのストレージ キーを再生成した場合、**[ストレージ キー]** ドロップダウンから目的のキーを再選択する必要があります。

![Redis の永続化][redis-cache-persistence-selected]

**[OK]** をクリックして永続化の構成を保存します。

バックアップ間隔が経過すると、次のバックアップ (または新しいキャッシュの最初のバックアップ) が開始されます。



## 永続化の FAQ

次の一覧は、Azure Redis Cache の永続化に関するよく寄せられる質問への回答です。

-	[以前に作成したキャッシュで永続化を有効にできますか](#can-i-enable-persistence-on-a-previously-created-cache)
-	[キャッシュの作成後にバックアップ頻度を変更できますか](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-	[バックアップ間隔を 60 分に設定した場合、バックアップの間隔が 60 分より長くなるのはなぜですか](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-	[新しいバックアップが作成されると、古いバックアップはどうなりますか](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-	[別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### 以前に作成したキャッシュで永続化を有効にできますか

はい、Redis の永続化はキャッシュの作成時と、既存の Premium キャッシュの両方に構成できます。

### キャッシュの作成後にバックアップ頻度を変更できますか

はい、**[Redis データ永続化]** ブレードでバックアップの頻度を変更できます。手順については、「[Redis の永続化を構成する](#configure-redis-persistence)」をご覧ください。

### バックアップ間隔を 60 分に設定した場合、バックアップの間隔が 60 分より長くなるのはなぜですか

バックアップ間隔は、前のバックアップ プロセスが正常に完了するまでは開始しません。バックアップ間隔を 60 分に設定し、バックアップ プロセスが正常に完了するのに 15 分かかる場合、次のバックアップは、前回のバックアップの開始時刻から 75 分経つまで開始しません。

### 新しいバックアップが作成されると、古いバックアップはどうなりますか

最新のものを除くすべてのバックアップは自動的に削除されます。この削除はすぐに行われないことがありますが、古いバックアップは無期限には保持されません。

### 別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか

-	大きいサイズにスケーリングした場合、影響はありません。
-	小さいサイズにスケーリングした場合、新しいサイズの[データベースの制限](cache-configure.md#databases)より大きなカスタムの[データベース](cache-configure.md#databases)設定が存在すると、そのデータベースのデータは復元されません。詳細については、「[スケーリング中に影響を受けるカスタム データベース](#is-my-custom-databases-setting-affected-during-scaling)」をご覧ください。
-	小さいサイズにスケーリングしていて、最新のバックアップからのデータをすべて保持するにはサイズが小さいためスペースが足りない場合、キーは復元プロセス中に削除されます。通常は [allkeys-lru](http://redis.io/topics/lru-cache) 削除ポリシーを使用します。

## 次のステップ
Premium キャッシュ機能をさらに使用する方法を学習します。

-	[Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_0921_2016-->