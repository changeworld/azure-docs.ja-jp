<properties 
	pageTitle="Premium Azure Redis Cache のデータ永続化の構成方法" 
	description="Premium レベルの Azure Redis Cache インスタンス用にデータの永続化を構成して管理する方法について説明します" 
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
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Premium Azure Redis Cache のデータ永続化の構成方法

Azure Redis Cache には、新しい Premium レベル (現在はプレビュー版) など、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。

Azure Redis Cache の Premium レベルには、クラスタリング、永続性、および Virtual Network のサポートが含まれています。この記事では、Premium Azure Redis Cache インスタンスで永続化を構成する方法について説明します。

その他の Premium キャッシュ機能については、「[Premium Azure Redis Cache のクラスタリングの構成方法](cache-how-to-premium-clustering.md)」および「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」を参照してください。

>[AZURE.NOTE]Azure Redis Cache Premium レベルは、現在プレビュー中です。プレビュー期間中は、永続化をクラスタリングまたは仮想ネットワークと組み合わせて使用することはできません。

## データの永続化とは
Redis の永続化を使用すると、Redis に格納されたデータを保持できます。また、スナップショットを取得したりデータをバックアップしたりして、ハードウェア障害のときに読み込むことができます。これは、Basic レベルや Standard レベルにはない大きな利点です。Basic/Standard レベルでは、すべてのデータはメモリに格納され、Cache ノードがダウンするような障害時にはデータが失われる可能性があります。

Azure Redis Cache の Redis 永続化では、データが Azure ストレージ アカウントに格納されます。パブリック プレビューでは [RDB モデル](http://redis.io/topics/persistence)がサポートされており、[AOF](http://redis.io/topics/persistence) はまもなくサポートされるようになります。

## データの永続化
永続化を構成すると、Azure Redis Cache は、Redis Cache のスナップショットを構成可能なバックアップ頻度に基づいて Redis バイナリ形式でディスクに保存します。プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュが再構築されます。

永続化は、キャッシュの作成中に **[Redis Cache の新規作成]** ブレードで構成します。キャッシュを作成するには、[Azure プレビュー ポータル](https://portal.azure.com)にサインインし、**[新規]**、**[データ + ストレージ]**、**[Redis Cache]** をクリックします。

![Redis Cache の作成][redis-cache-new-cache-menu]

永続化を構成するには、まず **[料金レベルの選択]** ブレードで **[Premium]** キャッシュのいずれかを選択します。

![料金レベルの選択][redis-cache-premium-pricing-tier]

Premium 料金レベルを選択した後、**[Redis の永続化]** をクリックします。

![Redis の永続化][redis-cache-persistence]

**[有効]** をクリックして RDB (Redis データベース) のバックアップを有効にします。

ドロップダウン リストから **[バックアップの頻度]** を選択します。選択肢は、**60 分**、**6 時間**、**12 時間**、**24 時間**です。前のバックアップ操作が正常に完了するとこの間隔のカウントダウンが開始し、期間が経過すると新しいバックアップが開始されます。

**[ストレージ アカウント]** をクリックして、使用するストレージ アカウントを選択します。**[ストレージ キー]** は自動的に設定されますが、ストレージ アカウントのキーを再生成した場合はここで更新できます。スループットが高いので、**Premium Storage** アカウントを使用することをお勧めします。

>[AZURE.NOTE]Premium レベルのプレビュー期間中は AOF を使用できませんが、Cache チームはこの機能を追加する作業を行っています。RDB と AOF の詳細およびそれぞれの利点については、[Redis の永続化に関するページ](http://redis.io/topics/persistence)を参照してください。

![Redis の永続化][redis-cache-persistence-selected]

**[OK]** をクリックして永続化の構成を保存します。

キャッシュが作成された後、バックアップ間隔が経過すると、最初のバックアップが開始されます。

## 永続化の FAQ

次の一覧は、Azure Redis Cache の永続化に関するよく寄せられる質問への回答です。

## 以前に作成したキャッシュで永続化を有効にできますか

プレビュー期間中は、Premium キャッシュを作成するときにのみ永続化を有効にして構成できます。パブリック プレビュー期間中は Basic/Standard から Premium へのスケーリングはサポートされませんが、近日中にサポートされるようになります。

## キャッシュの作成後にバックアップ頻度を変更できますか

プレビュー期間中は、キャッシュを作成するときにのみ永続化を構成できます。永続化の構成を変更するには、キャッシュを削除し、目的の永続化構成で新しいキャッシュを作成する必要があります。これはプレビューでの制限事項であり、まもなくサポートされるようになります。

## バックアップ間隔を 60 分に設定した場合、バックアップの間隔が 60 分より長くなるのはなぜですか

バックアップ間隔は、前のバックアップ プロセスが正常に完了するまでは開始しません。バックアップ間隔を 60 分に設定し、バックアップ プロセスが正常に完了するのに 15 分かかる場合、次のバックアップは、前回のバックアップの開始時刻から 75 分経つまで開始しません。

## 新しいバックアップが作成されると、古いバックアップはどうなりますか

最新のものを除くすべてのバックアップは自動的に削除されます。この削除はすぐに行われないことがありますが、古いバックアップは無期限には保持されません。

## 次のステップ
Premium キャッシュ機能をさらに使用する方法を学習します。

-	[How to configure clustering for a Premium Azure Redis Cache (Premium Azure Redis Cache のクラスタリングの構成方法)](cache-how-to-premium-clustering.md)
-	[Premium Azure Redis Cache に対する Virtual Network サポートの構成方法](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

<!---HONumber=Oct15_HO3-->