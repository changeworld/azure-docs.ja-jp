---
title: Azure Cosmos DB のプロビジョニング スループットの自動スケーリングについてよく寄せられる質問
description: Azure Cosmos DB データベースおよびコンテナーの自動スケーリングでプロビジョニングされたスループットについてよく寄せられる質問
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791784"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB の自動スケーリングでプロビジョニングされたスループットについてよく寄せられる質問

Azure Cosmos DB で、プロビジョニング スループットの自動スケーリングを使用すると、使用状況に応じてコンテナーまたはデータベースの RU/秒が自動的に管理およびスケーリングされます。 この記事では、自動スケーリング モードについてよく寄せられる質問に回答します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="is-autoscale-supported-for-all-apis"></a>すべての API で自動スケーリングがサポートされていますか?
はい。自動スケーリングは、すべての API でサポートされています: Core (SQL)、Gremlin、Table、Cassandra、および MongoDB 用 API。

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>自動スケーリングはマルチマスター アカウントでサポートされていますか?
はい。自動スケーリングはマルチマスター アカウントでサポートされています。 最大 RU/秒は、Cosmos アカウントに追加された各リージョンで利用できます。 

### <a name="what-is-the-pricing-for-autoscale-"></a>自動スケーリングの価格はいくらですか?
詳細については、Azure Cosmos DB の[価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>コンテナーまたはデータベースに対して自動スケーリングを有効にするにはどうすればよいですか?
自動スケーリングは、Azure portal を使用して作成された新しいコンテナーとデータベースで有効にすることができます。

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>プロビジョニング スループットの自動スケーリングを使用してコンテナーまたはデータベースを作成する機能は CLI または SDK でサポートされていますか?
現時点では、Azure portal からのみ、プロビジョニング スループットの自動スケーリングを使用してリソースを作成できます。 CLI と SDK のサポートはまだ提供されていません。

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>既存のコンテナーまたはデータベースに対して自動スケーリングを有効にできますか?
現時点では、新しいコンテナーとデータベースの作成時に自動スケーリングを有効にすることができます。 既存のコンテナーとデータベースに対して自動スケーリングを有効にするためのサポートはまだ提供されていません。 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) または[変更フィード](change-feed.md)を使用して、既存のコンテナーを新しいコンテナーに移行することができます。 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>コンテナーまたはデータベースに対する自動スケーリングをオフにすることはできますか?
はい。プロビジョニング スループットを "標準 (手動)" オプションに切り替えることで、自動スケーリングをオフにできます。 現在のリリースでは、自動スケーリングから標準プロビジョニング スループットに切り替えた後、同じリソースに対して自動スケーリングを再び有効にすることはできません。 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>自動スケーリングは共有スループット データベースでサポートされていますか?
はい。自動スケーリングは共有スループット データベースでサポートされています。 この機能を有効にするには、データベースを作成するときに、自動スケーリングと **[スループットのプロビジョニング]** オプションを選択します。 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>自動スケーリングが有効な場合、共有スループット データベースごとに許可されるコレクションの数はどのくらいですか?
自動スケーリングが有効になっている共有スループット データベースの場合、許可されるコレクションの数は次のとおりです:MIN(25, データベースの最大 RU/秒 / 1000)。 たとえば、データベースの最大スループットが 20,000 RU/秒の場合、そのデータベースは MIN(25, 20,000 RU/秒 / 1000) = 20 個のコレクションを保持できます。 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>各最大 RU/秒オプションに関連付けられたストレージの上限はどれだけですか?  
最大 RU/秒ごとのストレージの上限 (GB) は次のとおりです: データベースまたはコンテナーの最大 RU/秒 / 100。 たとえば、最大 RU/秒が 20,000 RU/秒の場合、リソースは 200 GB のストレージをサポートできます。 使用可能な最大 RU/秒およびストレージ オプションについては、[自動スケーリングの制限](provision-throughput-autoscale.md#autoscale-limits)に関する記事を参照してください。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>最大スループットに関連付けられているストレージの上限を超えるとどうなりますか?
データベースまたはコンテナーの最大スループットに関連付けられているストレージの上限を超えた場合、Azure Cosmos DB によって、そのストレージのレベルをサポートできる次の最上位レベルまで自動的に最大スループットが増加します。 たとえば、データベースまたはコンテナーが、4000 RU/秒の最大スループット オプションを使用してプロビジョニングされているとします。この場合、ストレージの上限は 50 GB です。 リソースのストレージが 100 GB に増加した場合、データベースまたはコンテナーの最大 RU/秒は自動的に 20,000 RU/秒に増加し、最大 200 GB をサポートできます。 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>自動スケーリングでは、トラフィックの急増に応じてスケールアップとスケールダウンがどれほど迅速に行われますか?
自動スケーリングでは、受信トラフィックに基づいて、最小および最大 RU/秒の範囲内で即座に RU/秒のスケールアップまたはスケールダウンを実行できます。 課金は 1 時間単位で行われます。この場合、特定の 1 時間の最大 RU/秒に対して課金されます。

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>自動スケーリングでは、カスタム最大スループット (RU/秒) の値を指定できますか?
現時点では、最大スループット (RU/秒) の [4 つのオプション](provision-throughput-autoscale.md#autoscale-limits)から選択できます。

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>データベースまたはコンテナーの最大 RU/秒を増やす (上位レベルへ移動する) ことはできますか? 
はい。 コンテナーの **[スケールと設定]** オプション、またはデータベースの **[スケール]** オプションから、自動スケーリングの上位の最大 RU/秒を選択できます。 これは非同期のスケールアップ操作であり、サービスは上位レベルのスケールをサポートする目的でより多くのリソースをプロビジョニングするため、完了までに時間がかかる場合があります (通常は、選択した RU/秒に応じて 4-6 時間かかります)。 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>データベースまたはコンテナーの最大 RU/秒を減らす (下位レベルへ移動する) ことはできますか?
はい。 データベースまたはコンテナーの現在のストレージが、スケールダウンする最大 RU/秒レベルに関連付けられた[ストレージの上限](#what-is-the-storage-limit-associated-with-each-max-rus-option)を下回っている限り、そのレベルまで最大 RU/秒を減らすことができます。 たとえば、最大 RU/秒として 20,000 RU/秒を選択した場合、ストレージが 50 GB 未満 (4000 RU/秒に関連付けられているストレージの上限) であれば、最大 RU/秒を 4000 RU/秒までスケールダウンすることができます。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒と物理パーティション間のマッピングとはどのようなものですか?
最初に最大 RU/秒を選択すると、Azure Cosmos DB によって次のようにプロビジョニングされます: 最大 RU/秒 / 10,000 RU/秒 = 物理パーティションの数。 各[物理パーティション](partition-data.md#physical-partitions)は、最大 10,000 RU/秒と 50 GB のストレージをサポートできます。 ストレージのサイズが大きくなると、Azure Cosmos DB によってパーティションが自動的に分割され、ストレージの増加に対応するように物理パーティションが追加されます。または、ストレージが[関連付けられた上限を超えている](#what-is-the-storage-limit-associated-with-each-max-rus-option)場合は、最大 RU/秒レベルが増加します。 

データベースまたはコンテナーの最大 RU/秒は、すべての物理パーティション全体で均等に分割されます。 そのため、1 つの物理パーティションでスケーリングできる合計スループットは次のようになります: データベースまたはコンテナーの最大 RU/秒 / 物理パーティションの数。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>受信要求がデータベースまたはコンテナーの最大 RU/秒を超えるとどうなりますか?
消費された RU/秒の全体がコンテナーまたはデータベースの最大 RU/秒を超えた場合、最大 RU/秒を超えた要求はスロットルされ、429 状態コードが返されます。 100% を超える正規化された使用率が発生する要求もスロットルされます。 正規化された使用率は、すべての物理パーティションにおける RU/秒の使用率の最大値として定義されます。 たとえば、最大スループットが 20,000 RU/秒で、P_1 および P_2 という 2 つの物理パーティションがあり、それぞれが 10,000 RU/秒にスケーリングできるとします。 ある 1 秒間で、P_1 が 6000 RU を使用し、P_2 が 8000 RU を使用した場合、正規化された使用率は MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8 になります。

> [!NOTE]
> Azure Cosmos DB クライアント SDK とデータ インポート ツール (Azure Data Factory、一括実行プログラム ライブラリ) は、429 で自動的に再試行をするため、不定期に 429 が発生しても問題はありません。 429 の数が多いままの場合は、最大 RU/秒を増やす必要があるか、または[ホット パーティション](#autoscale-rate-limiting)のパーティション分割方法を確認する必要があることを示しています。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 自動スケーリングが有効になっていても、429 (スロットリング/レート制限) が引き続き表示される可能性はありますか? 
はい。 2 つのシナリオで、429 が表示される可能性があります。 最初に、消費された RU/秒の全体がコンテナーまたはデータベースの最大 RU/秒を超えると、サービスはそれに応じて要求をスロットルします。 

2 番目に、ホット パーティションがある場合、つまり、他のパーティション キー値と比較して要求が過剰に増加する論理パーティション キー値がある場合、基になる物理パーティションが RU/秒の予算を超える可能性があります。 ベスト プラクティスとして、ホット パーティションを回避するには、ストレージとスループットの両方が均等に分散される[適切なパーティション キーを選択](partitioning-overview.md#choose-partitionkey)します。 

たとえば、20,000 RU/秒の最大スループットのオプションを選択し、4 つの物理パーティションを含む 200 GB のストレージを使用している場合、各物理パーティションは 5000 RU/秒に自動スケールアップできます。 特定の論理パーティション キーにホット パーティションがある場合、それが存在している基になる物理パーティションが 5000 RU/秒を超えると (つまり、100% の正規化された使用率を超えると)、429 が表示されます。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos コンテナーまたはデータベースで自動スケーリングを有効にする方法](provision-throughput-autoscale.md#create-db-container-autoscale)について学習します。
* [自動スケーリングを使用したプロビジョニング スループットの利点](provision-throughput-autoscale.md#autoscale-benefits)について学習します。
* [論理および物理パーティション](partition-data.md)の詳細について学習します。
