---
title: Azure Cache for Redis インスタンスを別のリージョンに移動する
description: Azure Cache for Redis インスタンスを別の Azure リージョンに移動する方法。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 8/27/2021
ms.openlocfilehash: 931c4aec854b2512c649570b5fe8fc24bfe11c81
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894901"
---
# <a name="move-azure-cache-for-redis-instances-to-different-regions"></a>Azure Cache for Redis インスタンスを別のリージョンに移動する

この記事では、Azure Cache for Redis インスタンスを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、リソースを別のリージョンに移動する場合があります。
- 新しい Azure リージョンを利用するため。
- 特定のリージョンでのみ使用できる機能やサービスをデプロイするため。
- 内部ポリシーとガバナンスの要件を満たすため。
- キャパシティ プランニングの要件に対応するため。

使用する Azure Cache for Redis のレベルによって、最適なオプションが決まります。

| キャッシュ レベル | オプション  | 
| ------------ |  ------- | 
| Premium | geo レプリケーション、新しいキャッシュの作成、2 つのキャッシュへのデュアル書き込み、または RDB ファイルを使用したデータのエクスポートとインポート| 
| Basic または Standard | 新しいキャッシュの作成、または 2 つのキャッシュへのデュアル書き込み| 
| Enterprise または Enterprise Flash | 新しいキャッシュの作成、または RDB ファイルを使用したデータのエクスポートとインポート | 

## <a name="geo-replication-premium"></a>geo レプリケーション (Premium)

### <a name="prerequisites"></a>前提条件 

2 つのキャッシュの間に geo レプリケーションを構成するには、次の前提条件を満たす必要があります。

- 両方のキャッシュが [Premium レベル](cache-overview.md#service-tiers)のキャッシュである。
- 両方のキャッシュが同じ Azure サブスクリプションに存在する。
- セカンダリ リンク キャッシュがプライマリ リンク キャッシュと同じキャッシュ サイズであるか、またはそれより大きいキャッシュ サイズである。
- 両方のキャッシュが既に存在し、実行中である。

### <a name="prepare"></a>準備

キャッシュ インスタンスを別のリージョンに移動するには、目的のリージョンに [2 番目の Premium キャッシュ インスタンスを作成する](quickstart-create-redis.md)必要があります。 両方のキャッシュが実行されたら、2 つのキャッシュ インスタンス間に geo レプリケーションを設定できます。 

> [!NOTE]
> Azure リージョン間のデータ転送は、標準の[帯域幅レート](https://azure.microsoft.com/pricing/details/bandwidth/)で課金されます。

geo レプリケーションでは一部の機能がサポートされていません。

- ゾーン冗長は geo レプリケーションではサポートされていません。
- 永続化は geo レプリケーションではサポートされていません。

geo レプリケーションのサポート条件:

- クラスタリングは、両方のキャッシュでクラスタリングが有効になっており、かつ同じ数のシャードが存在する場合にサポートされます。
- 異なる VNet 内のキャッシュは、注意事項付きでサポートされています。 詳細については、「[VNet 内の自分のキャッシュで geo レプリケーションを使用することはできますか](cache-how-to-geo-replication.md#can-i-use-geo-replication-with-my-caches-in-a-vnet)」を参照してください。

geo レプリケーションを構成した後、次の制限が、リンク キャッシュ ペアに適用されます。

- セカンダリ リンク キャッシュは読み取り専用です。 そこから読み取ることはできますが、データを書き込むことはできません。 
    - geo セカンダリ インスタンスからの読み取りを選択した場合は、geo プライマリと geo セカンダリの間で完全データ同期 (geo プライマリまたは geo セカンダリのどちらかが更新されたときや、一部の再起動シナリオなど) が実行されているときは必ず、geo プライマリと geo セカンダリの間の完全データ同期が完了するまで、geo セカンダリ インスタンスはそれに対する Redis 操作でエラーをスローします。
    - geo セカンダリから読み取るアプリケーションは、geo セカンダリがこのようなエラーをスローするたびに geo プライマリにフォールバックするように構築する必要があります。
- リンクが追加される前にセカンダリ リンク キャッシュにあったデータはすべて削除されます。 ただし、geo レプリケーションが後で削除された場合、レプリケートされたデータはセカンダリ リンク キャッシュに残ります。
- キャッシュがリンクされている間は、どちらのキャッシュも[スケーリング](cache-how-to-scale.md)できません。
- キャッシュでクラスタリングが有効になっている場合は、[シャードの数を変更](cache-how-to-premium-clustering.md)できません。
- どちらのキャッシュでも永続化を有効にすることはできません。
- どちらのキャッシュからも[エクスポート](cache-how-to-import-export-data.md#export)できます。
- セカンダリ リンク キャッシュには[インポート](cache-how-to-import-export-data.md#import)できません。
- キャッシュをリンク解除するまでは、リンクされたキャッシュ、またはそれらを含むリソース グループのどちらも削除できません。 詳しくは、「[リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか](cache-how-to-geo-replication.md#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)」をご覧ください。
- キャッシュが異なるリージョンに存在する場合、ネットワーク送信コストはリージョン間で移動されたデータに適用されます。 詳しくは、「[Azure リージョン間でデータをレプリケートするコストはどれくらいですか](cache-how-to-geo-replication.md#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)」をご覧ください。
- プライマリ リンク キャッシュとセカンダリ リンク キャッシュの間で自動フェールオーバーは発生しません。 詳細およびクライアント アプリケーションをフェールオーバーする方法については、「[セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか](cache-how-to-geo-replication.md#how-does-failing-over-to-the-secondary-linked-cache-work)」を参照してください。

### <a name="move"></a>詳細ビュー

1. geo レプリケーションのために 2 つのキャッシュをリンクするには、まずプライマリ リンク キャッシュにするキャッシュのリソース メニューの **[geo レプリケーション]** をクリックします。 次に、左側の **[geo レプリケーション]** の **[キャッシュ レプリケーション リンクの追加]** をクリックします。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="リンクの追加":::

1. **[互換性のあるキャッシュ]** の一覧で目的のセカンダリ キャッシュの名前を選択します。 そのセカンダリ キャッシュが一覧に表示されていない場合は、セカンダリ キャッシュの [geo レプリケーションの前提条件](#prerequisites)が満たされていることを確認します。 キャッシュをリージョンでフィルター処理するには、マップ内のリージョンを選択して、 **[互換性のあるキャッシュ]** の一覧にあるキャッシュのみを表示します。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="geo レプリケーションの互換性のあるキャッシュ":::

    また、コンテキスト メニューを使用してリンク プロセスを開始したり、セカンダリ キャッシュに関する詳細を表示したりすることもできます。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="geo レプリケーションのコンテキスト メニュー":::

1. **[リンク]** を選択して、2 つのキャッシュをリンクし、レプリケーション プロセスを開始します。
   
    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="キャッシュのリンク":::

### <a name="verify"></a>確認

1. 左側の **[geo レプリケーション]** を使用して、レプリケーション プロセスの進行状況を表示できます。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="リンクの状態":::

    プライマリ キャッシュとセカンダリ キャッシュの両方の **[概要]** を使用して、左側にリンクの状態を表示することもできます。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="プライマリ キャッシュとセカンダリ キャッシュのリンク状態を表示する方法を強調表示するスクリーンショット。":::

    レプリケーション プロセスが完了すると、 **[リンクの状態]** が「**成功**」に変わります。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="キャッシュの状態":::

    プライマリ リンク キャッシュは、リンク プロセス中も引き続き使用できます。 セカンダリ リンク キャッシュは、リンク プロセスが完了するまで使用できません。

### <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする 

ターゲット リージョン内の新しいキャッシュに必要なすべてのデータが設定された後、2 つのキャッシュ間のリンクを削除し、元のインスタンスを削除します。

1. 2 つのキャッシュ間のリンクを削除し、geo レプリケーションを停止するには、左側の **[geo レプリケーション]** で **[キャッシュのリンク解除]** をクリックします。

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="[Unlink caches]\(キャッシュのリンク解除\)":::

    リンク解除プロセスが完了すると、セカンダリ キャッシュが読み取りと書き込みの両方に対して使用可能になります。

>[!NOTE]
>geo レプリケーション リンクが削除された場合も、プライマリ リンク キャッシュからレプリケートされたデータはセカンダリ キャッシュに残ります。
>
>

2. 元のインスタンスを削除します。

## <a name="create-a-new-cache-all-tiers"></a>新しいキャッシュを作成する (すべてのレベル)

### <a name="prerequisites"></a>前提条件
- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

### <a name="prepare"></a>準備
移動中にデータを維持する必要がない場合、リージョンを移動する最も簡単な方法は、ターゲット リージョンに新しいキャッシュ インスタンスを作成し、アプリケーションを接続することです。 たとえば、Redis をデータベース レコードのルックアサイド キャッシュとして使用すると、キャッシュをゼロから簡単に再構築できます。

### <a name="move"></a>詳細ビュー

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

最後に、新しいインスタンスが使用されるようにアプリケーションを更新します。 

### <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする 
ターゲット リージョンで新しいキャッシュが実行されたら、元のインスタンスを削除します。


## <a name="export-and-import-data-with-an-rdb-file-premium-enterprise-enterprise-flash"></a>RDB ファイルを使用したデータのエクスポートとインポート (Premium、Enterprise、Enterprise Flash)
オープンソースの Redis は、キャッシュのメモリ内データセットのスナップショットを取得してファイルに保存するための標準的なメカニズムが定義されます。 この (RDB と呼ばれる) ファイルは、別の Redis キャッシュで読み取ることもできます。 [Azure Cache for Redis の Premium と Enterprise](cache-overview.md#service-tiers) では、RDB ファイルを使用したキャッシュ インスタンスへのデータのインポートがサポートされています。 RDB ファイルを使用すると、既存のキャッシュから Azure Cache for Redis にデータを転送できます。

> [!IMPORTANT]
> RDB ファイルの形式は、Redis のバージョン間で変更されることがあるため、下位互換性が確保されない場合があります。 エクスポートするキャッシュの Redis のバージョンは、新しいキャッシュ インスタンスのバージョンと同じかそれより前である必要があります。
>

### <a name="prerequisites"></a>前提条件
- 両方のキャッシュが [Premium レベルまたは Enterprise レベル](cache-overview.md#service-tiers)のキャッシュである。
- 2 番目のキャッシュは、元のキャッシュと同じキャッシュ サイズまたはそれよりも大きいキャッシュ サイズのどちらかです。
- エクスポートするキャッシュの Redis のバージョンは、新しいキャッシュ インスタンスのバージョンと同じかそれより前である必要があります。

### <a name="prepare"></a>準備
キャッシュ インスタンスを別のリージョンに移動するには、目的のリージョンに [2 番目の Premium キャッシュ インスタンス](quickstart-create-redis.md)または [2 番目の Enterprise キャッシュ インスタンス](quickstart-create-redis-enterprise.md)を作成する必要があります。

### <a name="move"></a>詳細ビュー
1. Azure Cache for Redis でデータをインポートまたはエクスポートする方法の詳細については、[こちら](cache-how-to-import-export-data.md)を参照してください。

2. 新しいキャッシュ インスタンスが使用されるようにアプリケーションを更新します。

### <a name="verify"></a>確認
インポート操作の進行状況を監視するには、Azure Portal からの通知を確認するか、[監査ログ](../azure-monitor/essentials/activity-log.md)のイベントを確認します。

### <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする 
ターゲット リージョンで新しいキャッシュが実行されたら、元のインスタンスを削除します。

## <a name="dual-write-to-two-caches-basic-standard-and-premium"></a>2 つのキャッシュへのデュアル書き込み (Basic、Standard、Premium)
キャッシュ間で直接データを移動するのではなく、アプリケーションを使用して、既存のキャッシュと設定中の新しいものの両方にデータを書き込むことができます。 最初に、アプリケーションによって既存のキャッシュからデータが読み取られます。 新しいキャッシュに必要なデータがある場合は、そのキャッシュにアプリケーションを切り替え、古いキャッシュを削除します。 たとえば、Redis をセッション ストアとして使用し、アプリケーション セッションが 7 日間有効であるとします。 1 週間で 2 つのキャッシュに書き込むと、新しいキャッシュに期限切れではないセッション情報がすべて含まれていることがわかります。 その時点以降は、データが失われることを心配せずに安全に利用できます。

### <a name="prerequisites"></a>前提条件
- 2 番目のキャッシュは、元のキャッシュと同じキャッシュ サイズまたはそれよりも大きいキャッシュ サイズのどちらかです。

### <a name="prepare"></a>準備
キャッシュ インスタンスを別のリージョンに移動するには、目的のリージョンに [2 番目のキャッシュ インスタンスを作成する](quickstart-create-redis.md)必要があります。

### <a name="move"></a>詳細ビュー
このオプションを実装する一般的な手順は、次のとおりです。

1. 新しいインスタンスと元のインスタンスの両方に書き込まれるようにアプリケーション コードを変更します。

2. 新しいインスタンスに十分なデータが入力されるまで、元のインスタンスからのデータの読み取りを続行します。

3. 新しいインスタンスからの読み取りと書き込みのみが行われるようにアプリケーション コードを更新します。

### <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする 
ターゲット リージョンで新しいキャッシュが実行されたら、元のインスタンスを削除します。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について
- [geo レプリケーションに関する FAQ](cache-how-to-geo-replication.md#geo-replication-faq)
- [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
- [Azure Cache for Redis の高可用性](cache-high-availability.md)


