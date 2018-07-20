---
title: Azure Cosmos DB を使用したデータのグローバル分散 | Microsoft Docs
description: グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB のグローバル データベースを使用した、地球規模の geo レプリケーション、フェールオーバー、データ復旧について説明します。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: dec981ad750a49646916dbef40a4cc632ab71da2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856642"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB を使用してデータをグローバルに分散させる方法
Azure はユビキタスです。50 か所以上の地理的リージョンでグローバル展開されており、継続的に拡大しています。 その世界的なプレゼンスにより、Azure が開発者に提供する、他とは異なる機能の 1 つがグローバル分散アプリケーションの簡単な構築、デプロイ、そして管理を実現する能力です。 

[Azure Cosmos DB](../cosmos-db/introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散型マルチモデル データベース サービスです。 Azure Cosmos DB は、ターン キー グローバル分散、[スループットとストレージの世界規模でのエラスティック スケーリング](../cosmos-db/partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性モデル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの包括性を持った SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB では、[すべてのデータのインデックスが自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)されるため、ユーザーがスキーマやインデックスの管理に対処する必要はありません。 また、マルチモデル サービスでもあり、ドキュメント、キー値、グラフ、列ファミリのデータ モデルに対応しています。 Azure Cosmos DB は、クラウドのためにクラウドで構築されたサービスとして、初期段階からマルチテナントとグローバル分散を想定して入念に設計されています。


![パーティション分割され、3 つのリージョンに分散された Azure Cosmos DB コンテナー](./media/distribute-data-globally/global-apps.png)

**1 つの Azure Cosmos DB コンテナーをパーティション分割し、複数の Azure リージョンに分散**

Azure Cosmos DB を構築することで学んだように、グローバル分散を後付けすることはできません。 "単一サイト" のデータベース システムに "上乗せ" することはできないのです。 グローバル分散データベースが提供する機能は、"単一サイト" データベースが提供する従来の地理的なディザスター リカバリー (Geo-DR) の機能の枠を越えて多岐にわたります。 Geo-DR 機能を提供する単一サイト データベースは、グローバル分散データベースの厳密なサブセットです。 

Azure Cosmos DB のターンキー グローバル分散により、開発者はデータベース ログ上でラムダ パターン ([AWS DynamoDB レプリケーション](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)など) を使用するか、複数のリージョン間で "二重書き込み" を実行することによって、レプリケーションの独自のスキャフォールディングを構築する必要はありません。 このような方法の正確性を保証しながら、厳密な SLA を提供することは不可能であるため、これらの方法はお勧め*しません*。 

この記事では、Azure Cosmos DB のグローバル分散機能の概要を説明します。 また、Azure Cosmos DB の包括的な SLA を提供する独自のアプローチについても説明します。 

## <a id="EnableGlobalDistribution"></a>ターンキー グローバル配布の有効化
Azure Cosmos DB には、世界各地に分散したアプリケーションを簡単に作成できるようにするための以下の機能が用意されています。 これらの機能は、Azure Cosmos DB のリソース プロバイダー ベースの [REST API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) と Azure Portal で使用できます。

次の動画で、Azure Cosmos DB の、簡単に使用できるグローバル分散機能をご確認ください。

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>ユビキタスなリージョン プレゼンス 
Azure では、[新しいリージョン](https://azure.microsoft.com/regions/)をオンラインにすることで、地理的プレゼンスを常に高めています。 Azure Cosmos DB は、Azure の *基本サービス*として位置付けられており、すべての新しい Azure リージョンで既定サービスとして提供されています。 これにより、Azure がビジネス向けの新しいリージョンを開設するとすぐに、地理的リージョンを Azure Cosmos DB データベース アカウントに関連付けることができます。

### <a id="UnlimitedRegionsPerAccount"></a>Azure Cosmos DB データベース アカウントに無制限の数のリージョンを関連付ける
Azure Cosmos DB では、Azure Cosmos DB データベース アカウントに Azure リージョンをいくつでも関連付けることができます。 ジオフェンスの制限 (中国、ドイツなど) を除き、Azure Cosmos DB データベース アカウントに関連付けることができるリージョンの数に制限はありません。 次の図は、25 か所の Azure リージョンにまたがるように構成されたデータベース アカウントを示しています。  

![25 か所の Azure リージョンにまたがる Azure Cosmos DB データベース アカウント](./media/distribute-data-globally/spanning-regions.png)

**25 か所の Azure リージョンにまたがるテナントの Azure Cosmos DB データベース アカウント**


### <a id="PolicyBasedGeoFencing"></a>ポリシー ベースのジオフェンス
Azure Cosmos DB は、ポリシー ベースのジオフェンス機能に対応するよう設計されています。 ジオフェンスは、データ ガバナンスとコンプライアンスの制限を保証し、アカウントへの特定のリージョンの関連付けを防ぐことができる重要なコンポーネントです。 ジオフェンスの例として、グローバル分散の範囲をソブリン クラウド内 (中国、ドイツなど) または政府の課税境界内 (オーストラリアなど) のリージョンに設定することが挙げられます。 ポリシーは、Azure サブスクリプションのメタデータを使用して制御されます。

### <a id="DynamicallyAddRegions"></a>リージョンを動的に追加および削除する
Azure Cosmos DB では、データベース アカウントから、リージョンの追加 (関連付け) とリージョンの削除 (関連付け解除) をいつでも行うことができます ([前の図](#UnlimitedRegionsPerAccount)を参照)。 Azure Cosmos DB では複数のパーティションにまたがって並行してデータをレプリケートできるため、新しいリージョンが追加されたときに、世界のどこででも 30 分以内に操作を開始できます (データが 100 TB 以下の場合)。 

### <a id="FailoverPriorities"></a>フェールオーバーの優先順位
複数のリージョンで障害が発生したときにリージョン内でフェールオーバーのシーケンスを正確に制御するために、Azure Cosmos DB ではデータベース アカウントに関連付けられたさまざまなリージョンに*優先順位*を関連付けることができます (次の図を参照)。 Azure Cosmos DB では、指定された優先順位で自動フェールオーバー シーケンスが発生します。 リージョン内フェールオーバーの詳細については、「[Automatic regional failovers for business continuity in Azure Cosmos DB (Azure Cosmos DB でのビジネス継続性のためのリージョン内自動フェールオーバー)](regional-failover.md)」をご覧ください。


![Azure Cosmos DB でのフェールオーバーの優先順位の構成](./media/distribute-data-globally/failover-priorities.png)

**Azure Cosmos DB のテナントは、データベース アカウントに関連付けられているリージョンのフェールオーバーの優先順位を構成することが可能 (右側のウィンドウ)**

### <a id="ConsistencyLevels"></a>グローバル分散データベースの明確に定義された複数の整合性モデル
Azure Cosmos DB は、SLA に従って、[複数の詳細に定義された、直感的かつ実用的な整合性モデル](consistency-levels.md)を提供します。 ワークロードやシナリオに応じて、(使用可能なオプションの一覧から) 特定の整合性モデルを選択できます。 

### <a id="TunableConsistency"></a>グローバルにレプリケートされたデータベースの調整可能な整合性
Azure Cosmos DB では、実行時に、要求ごとに既定の整合性レベルをプログラムを使用して上書きし、緩和することができます。 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>動的に構成可能な読み取りリージョンと書き込みリージョン
Azure Cosmos DB では、"読み取り" リージョン、"書き込み" リージョン、"読み取り/書き込み" リージョンとして (データベースに関連付けられた) リージョンを構成できます。 

### <a id="ElasticallyScaleThroughput"></a>Azure リージョン間でスループットを弾力的にスケーリングする
プログラミングによりスループットをプロビジョニングすることで、Azure Cosmos DB コンテナーを弾力的にスケーリングできます。 スループットは、Azure Cosmos DB コンテナーが配布されているすべてのリージョンに適用されます。

### <a id="GeoLocalReadsAndWrites"></a>地理的にローカルの読み取りと書き込み
グローバル分散データベースの主な利点は、世界中のどこでも短い待機時間でデータにアクセスできることです。 Azure Cosmos DB では、低待機時間の読み取りを提供し、世界中に 99 パーセンタイルで書き込みます。 これにより、最も近いローカル リージョンからすべての読み取り操作が実行されます。 読み取り要求を処理するために、読み取りが発行されたリージョンに対してローカルのクォーラムが使用されます。 書き込みの場合も同様です。 レプリカの大半が書き込みをローカルで永続的にコミットした後にのみ、書き込みの確認応答が返されます。ただし、書き込みの確認応答を行う際にリモート レプリカでゲート制御は行われません。 言い換えると、Azure Cosmos DB のレプリケーション プロトコルは、読み取りクォーラムと書き込みクォーラムが、それぞれ要求が発行された読み取りおよび書き込みリージョンに対して常にローカルであるという前提の下で動作します。

### <a id="ManualFailover"></a>手動フェールオーバー
Azure Cosmos DB では、(データベースの範囲を超えて) アプリケーション全体の*エンド ツー エンド*の可用性プロパティを検証するために、データベース アカウントのフェールオーバーをトリガーできます。 障害検出とリーダー選定の安全性プロパティと存続性プロパティの両方が保証されるため、Azure Cosmos DB ではテナントによる手動フェールオーバー操作で*データ損失のないこと*が保証されます。

### <a id="AutomaticFailover"></a>自動フェールオーバー
Azure Cosmos DB では、1 つ以上のリージョンで障害が発生したときのために、自動フェールオーバーがサポートされています。 リージョン内フェールオーバー時には、読み取り待機時間、アップタイムの可用性、整合性、スループットの各 SLA が維持されます。 Azure Cosmos DB では、自動フェールオーバー操作が完了するまでの期間の上限値を設定できます。 これは、リージョンでの障害発生時にデータが失われる可能性がある期間です。

### <a id="GranularFailover"></a>フェールオーバーのさまざまな粒度に対応する設計
現在、自動および手動フェールオーバー機能はデータベース アカウントの粒度で公開されています。 内部的には、Azure Cosmos DB は、データベース、コンテナー、または (さまざまなキーを所有するコンテナーの) パーティションというさらに細かい粒度で*自動*フェールオーバーを提供するように設計されています。 

### <a id="MultiHomingAPIs"></a>Azure Cosmos DB のマルチホーム
Azure Cosmos DB では、*論理* (リージョンに依存しない) エンドポイントまたは*物理* (リージョン固有の) エンドポイントを使用してデータベースを操作できます。 論理エンドポイントを使用すると、フェールオーバーが発生した場合にアプリケーションを透過的にマルチホームにすることができます。 後者の物理エンドポイントは、読み取りと書き込みを特定のリージョンにリダイレクトするため、アプリケーションは、きめ細かく制御できます。

[SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md)、[Table API](../cosmos-db/tutorial-global-distribution-table.md)、[MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) の各 API の読み取り設定を構成する方法については、各リンク先の記事をご覧ください。

### <a id="TransparentSchemaMigration"></a>データベース スキーマとインデックスの透過的で一貫性のある移行 
Azure Cosmos DB は完全に[スキーマ非依存](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)です。 独自のデータベース エンジン設計により、Azure Cosmos DB は、スキーマやセカンダリ インデックスを使用することなく、取り込んだすべてのデータのインデックスを自動的かつ同期的に作成できます。 これにより、データベース スキーマとインデックスの移行を気にしたり、スキーマの変更の複数フェーズのアプリケーション ロールアウトを調整したりしなくても、グローバル分散アプリケーションを迅速に反復処理できます。 Azure Cosmos DB では、ユーザーによるインデックス作成ポリシーの明示的な変更によって、パフォーマンスや可用性が低下しないことが保証されます。  

### <a id="ComprehensiveSLAs"></a>(可用性の枠を越えた) 包括的な SLA
グローバル分散型データベース サービスである Azure Cosmos DB は、データベースに関連付けられたリージョンの数に関係なく、**可用性**、**待機時間**、**スループット**、**整合性**が明確に定義された包括的な SLA を提供します。  

## <a id="LatencyGuarantees"></a>待機時間の保証
Azure Cosmos DB のようなグローバル分散型データベース サービスの主な利点は、世界中のどこでもデータへの待機時間の短いアクセスを実現できることです。 Azure Cosmos DB では、各種データベース操作に対して 99 パーセンタイルの低待機時間が保証されます。 Azure Cosmos DB で採用されているレプリケーション プロトコルにより、データベース操作 (読み取りと書き込みの両方) は、常にクライアントのリージョンに対してローカルのリージョンで実行されます。 Azure Cosmos DB の待機時間の SLA には、読み取り、(同期的に) インデックスを使用した書き込み、さまざまな要求/応答サイズのクエリの 99 パーセンタイルの保証が含まれます。 書き込みの待機時間の保証には、ローカル リージョン内でのマジョリティ クォーラムの永続的なコミットが含まれます。

### <a id="LatencyAndConsistency"></a>待機時間と整合性の関係 
世界各地に分散したサービスの整合性を保証するには、書き込みを同期的に複製したり、複数のリージョンにまたがる読み取りを同期的に実行する必要があります。 光通信の速度とワイド エリア ネットワークの信頼性のみに依存すると、データベース操作の待機時間が長くなり、可用性が低下します。 そのため、すべての単一リージョンのアカウントとすべてのマルチリージョンのアカウントに対して、緩やかな整合性で、99 パーセンタイルの低待機時間と 99.99% の可用性を保証し、かつすべての複数リージョンのデータベース アカウントに 99.999% の可用性を保証するには、サービスで非同期のレプリケーションを使用する必要があります。 この場合、サービスは[明確に定義された緩やかな整合性モデル](consistency-levels.md)も提供する必要があります。厳密な整合性よりも緩やかであり (低待機時間と可用性を保証するため)、なおかつ "最終的な" 整合性よりも厳密である (直感的なプログラミング モデルを使用する) のが理想的です。

Azure Cosmos DB では、特定の整合性レベルの保証を実現するために、読み取り操作で複数のリージョンのレプリカに接続する必要はありません。 同様に、すべてのリージョンでデータがレプリケートされている間、書き込み操作がブロックされることもありません (つまり、書き込みはリージョン間で非同期にレプリケートされます)。 マルチリージョンのデータベース アカウントでは、厳格な整合性レベルと複数の緩やかな整合性レベルの両方を利用できます。 

### <a id="LatencyAndAvailability"></a>待機時間と可用性の関係 
待機時間と可用性は表裏一体です。 安定状態での操作の待機時間と、障害発生時の可用性、およびネットワーク パーティションについて説明します。 アプリケーションの観点から言えば、低速で実行されているデータベース操作は使用不可の状態のデータベースと区別できません。 

長い待機時間と使用不可の状態を区別するために、Azure Cosmos DB では各種データベース操作の待機時間に絶対上限を設けています。 データベース操作に時間がかかり、待機時間の上限を超えた場合、Azure Cosmos DB はタイムアウト エラーを返します。 Azure Cosmos DB の可用性の SLA により、タイムアウトは SLA と照らし合わせてカウントされます。 

### <a id="LatencyAndThroughput"></a>待機時間とスループットの関係
Azure Cosmos DB では、待機時間とスループットのどちらかを選択する必要はありません。 SLA によって 99 パーセンタイルの待機時間と、プロビジョニングされたスループットの提供の両方が実現されます。 

## <a id="ConsistencyGuarantees"></a>整合性の保証
[厳密な整合性モデル](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)はデータ プログラミングの標準基準ですが、(安定状態での) 待機時間が長く、(障害発生時の) 可用性が低いという重大な欠点があります。 

Azure Cosmos DB は、レプリケートされたデータの整合性を推測するために、明確に定義されたプログラミング モデルを提供します。 マルチホーム機能を備え、世界中に分散したアプリケーションを簡単に構築できるように、Azure Cosmos DB で使用されている整合性モデルは、読み取りおよび書き込み操作が処理される場所のリージョンに依存しないよう設計されています。 

Azure Cosmos DB の整合性 SLA は、読み取り要求の 100% が、ユーザーによって指定された整合性モデル (データベース アカウントまたは要求レベルにおいて) の整合性保証を満たしていることを保証します。 整合性レベルに関連付けられている整合性の保証がすべて満たされていれば、読み取り要求は整合性の SLA を満たしていると見なされます。 次の表は、Azure Cosmos DB で提供される特定の整合性モデルに対応する整合性の保証を示しています。

<table>
    <tr>
        <td><strong>整合性モデル</strong></td>
        <td><strong>整合性の特性</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>Strong</td>
        <td>線形化可能</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness</td>
        <td>モノトニックな読み取り (リージョン内)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>有界整合性制約 &lt; K、T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">Session</td>
        <td>独自の書き込みの読み取り</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>モノトニックな読み取り</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
</table>

**Azure Cosmos DB の特定の整合性レベルに関連付けられた整合性の保証**


### <a id="ConsistencyAndAvailability"></a>整合性と可用性の関係
[CAP 定理](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)の[不可能性の結果](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)は、障害発生時にシステムが可用性を維持しながら、線形化可能な整合性を提供することが実際に不可能であることを証明しています。 データベース サービスは、CP または AP のいずれかから選択する必要があります。CP システムは、線形化可能な整合性が高い代わりに可用性が低く、AP システムでは可用性が高い代わりに[線形化可能な整合性](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)が低くなります。 Azure Cosmos DB は要求された整合性レベルに違反することはないため、形式上は CP システムになります。 ただし、整合性は実際にはそれほど単純ではなく、明確に定義された整合性モデルは、線形化可能な整合性と最終的な整合性の間の整合性領域の中間に複数存在します。 Azure Cosmos DB では、現実的なシナリオに適用することができ、直観的に使用できる緩やかな整合性モデルが複数特定されています。 Azure Cosmos DB は、[緩やかではあるが明確に定義された複数の整合性モデル](consistency-levels.md)と、すべての単一リージョンのアカウントとすべてのマルチリージョンのアカウントに対する 99.99% の可用性、およびすべてのマルチリージョンのデータベース アカウントへの 99.999% の読み取り可用性を提供することで、整合性と可用性のバランスを取っています。 

### <a id="ConsistencyAndAvailability"></a>整合性と待機時間の関係
CAP のより包括的なバリエーションは [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) と呼ばれ、ここでも、安定状態での待機時間と整合性のバランスが考慮されています。 PACELC では、安定状態では、データベース システムは整合性か待機時間のいずれかを選択しなければならないとされています。 Azure Cosmos DB では、(非同期レプリケーションとローカルの読み取り/書き込みクォーラムによって支えられた) 複数の緩やかな整合性モデルを採用することにより、すべての読み取りと書き込みが、それぞれ読み取りリージョンと書き込みリージョンに対してローカルであることが保証されています。 これにより、Azure Cosmos DB は特定の整合性レベルに対してリージョン内の低待機時間を保証できます。  

### <a id="ConsistencyAndThroughput"></a>整合性とスループットの関係
特定の整合性モデルの実装は、選択した[クォーラムの種類](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)によって異なるため、スループットも選択した整合性によって異なります。 たとえば、Azure Cosmos DB では、厳密な整合性の読み取りでの RU 料金は、最終的な整合性の読み取りの場合の約 *2 倍*になります。 この場合、同じスループットを実現するために、2 倍の RU をプロビジョニングする必要があります。


![整合性とスループットの関係](./media/distribute-data-globally/consistency-and-throughput.png)

**Azure Cosmos DB における特定の整合性モデルと読み取り能力の関係**

## <a id="ThroughputGuarantees"></a>スループットの保証 
Azure Cosmos DB では、必要または需要に応じて、無制限の数のリージョンにまたがってスループット (およびストレージ) を弾力的にスケーリングできます。 

![パーティション分割されて分散された Azure Cosmos DB コンテナー](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**(1 つのリージョン内の 3 つのリソース パーティションに) 水平にパーティション分割され、3 つの Azure リージョンにわたって世界各地に配布された単一の Azure Cosmos DB コンテナー**

Azure Cosmos DB コレクションは、2 つのディメンション (i) 1 つのリージョン内、および (ii) 複数のリージョン、にわたって配布されます。 その方法は次のとおりです。 

* **ローカル配布**: 1 つのリージョン内で、Azure Cosmos DB コンテナーは*リソース パーティション*単位で水平にスケールアウトされます。 各リソース パーティションは、キーのセットを管理し、別名*レプリカ セット*と呼ばれる 4 つのレプリカと、それらのレプリカ間のステート マシン複製によって物理的に表わされるため、厳密な整合性と高い可用性を兼ね備えています。 Azure Cosmos DB は、リソース パーティションが、割り当てられたシステム リソースの予算に合わせてスループットの共有を提供する役割を担う、リソースによって完全に管理されたシステムです。 Azure Cosmos DB コンテナーのスケーリングは、ユーザーには透過的に実行されます。 リソース パーティションの管理は Azure Cosmos DB によって管理され、ストエージやスループットの要件の変化に従って分割と結合が実行されます。 
* **グローバル配信**: 複数のリージョンにわたるデータベースの場合、各リソース パーティションはそれらのリージョンに配信されます。 さまざまなリージョンでキーの同じセットを所有するリソース パーティションが、*パーティション セット*を形成します ([前の図](#ThroughputGuarantees)を参照)。  パーティション セット内のリソース パーティションは、データベースに関連付けられた複数のリージョンにわたってステート マシン レプリケーションを使用して調整されます。 構成されている整合性レベルに応じて、さまざまなトポロジ (スター、デイジーチェーン、ツリーなど) を使用してパーティション セット内のリソース パーティションが動的に構成されます。 

Azure Cosmos DB の応答性の高いパーティション管理、負荷分散、厳密なリソース ガバナンス機能により、Azure Cosmos DB コンテナーまたはデータベースに関連付けられた複数の Azure リージョンにわたってスループットを弾力的にスケーリングできます。 Azure Cosmos DB では、プロビジョニングされたスループットをランタイム時に変更できます。 他のデータベース操作と同様に、Azure Cosmos DB は、プロビジョニングされたスループットを変更する要求の待機時間の絶対上限を保証します。 一例として、次の図は需要に基づいてスループット (2 つのリージョンでの 1 秒あたりの要求数が 1 M ～ 10 M) が弾力的にプロビジョニングされた顧客のコンテナーを示しています。

![スループットが弾力的にプロビジョニングされた Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

**スループット (1 秒あたりの要求数が 1 M ～ 10 M) が弾力的にプロビジョニングされた顧客のコンテナー**

### <a id="ThroughputAndConsistency"></a>スループットと整合性の関係 
「[整合性とスループットの関係](#ConsistencyAndThroughput)」と同じです。

### <a id="ThroughputAndAvailability"></a>スループットと可用性の関係
プロビジョニングされたスループットが変更されても、Azure Cosmos DB は高可用性を維持し続けます。 Azure Cosmos DB では、操作によってパフォーマンスや可用性が低下しないよう、リソース パーティションが透過的に管理され (分割操作、結合操作、複製操作など)、スループットはアプリケーションによって弾力的に増減されます。 

## <a id="AvailabilityGuarantees"></a>可用性の保証
Azure Cosmos DB は、すべての単一リージョンのデータベース アカウントとすべてのマルチリージョンのアカウントに対して緩やかな整合性で 99.99% の可用性 SLA を提供し、すべてのマルチリージョンのデータベース アカウントに 99.999% の可用性を提供します。 前述のように、Azure Cosmos DB の可用性の保証には、各データ プレーン操作とコントロール プレーン操作の待機時間の絶対上限が含まれます。 可用性の保証が、リージョンの数やリージョン間の地理的距離によって変わることはありません。 可用性の保証は、手動フェールオーバーと自動フェールオーバーの両方の側面に適用されます。 Azure Cosmos DB は、アプリケーションが論理エンドポイントに対して動作することができ、フェールオーバーが発生した場合に要求を新しいリージョンに透過的にルーティングできるようにするための透過的なマルチホーム API を提供します。 リージョン内フェールオーバー時にアプリケーションを再デプロイする必要はなく、可用性の SLA は常に守られます。

### <a id="AvailabilityAndConsistency"></a>可用性と整合性、待機時間、スループットの関係
可用性と整合性、待機時間、スループットの関係については、「[整合性と可用性の関係](#ConsistencyAndAvailability)」、「[待機時間と可用性の関係](#LatencyAndAvailability)」、「[スループットと可用性の関係](#ThroughputAndAvailability)」をご覧ください。 

## <a id="CustomerFacingSLAMetrics"></a>顧客向け SLA メトリック
Azure Cosmos DB では、スループット、待機時間、整合性、可用性のメトリックを透過的に公開しています。 これらのメトリックには、プログラミングまたは Azure Portal を使用してアクセスできます (次の図を参照)。 また、Azure Application Insights を使用して、さまざまなしきい値にアラートを設定することもできます。
 

![Azure Cosmos DB の顧客が表示できる SLA メトリック](./media/distribute-data-globally/customer-slas.png)

**整合性、待機時間、スループット、可用性のメトリックを各テナントに透過的に提供**

## <a id="Next Steps"></a>次のステップ
* Azure Portal を使用して Azure Cosmos DB アカウントにグローバル レプリケーションを実装する方法については、[Azure Portal を使用して Azure Cosmos DB グローバル データベース レプリケーションを実行する方法](tutorial-global-distribution-sql-api.md)に関する記事をご覧ください。
* Azure Cosmos DB を使用してマルチマスター アーキテクチャを実装する方法については、[Azure Cosmos DB を使用したマルチマスター データベース アーキテクチャ](multi-region-writers.md)に関する記事をご覧ください。
* Azure Cosmos DB の自動および手動フェールオーバーのしくみの詳細については、[Azure Cosmos DB のリージョン内フェールオーバー](regional-failover.md)に関する記事をご覧ください。

## <a id="References"></a>参考資料
1. Eric Brewer 著:  「[Towards Robust Distributed Systems (堅牢な分散システムに向けて)](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)」
2. Eric Brewer 著:  「[CAP Twelve Years Later: How the "Rules" have changed (CAP の 12 年後: "ルール" がどのように変わったか)](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)」
3. Gilbert、Lynch 共著: 「 - [Brewer&#39;s Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services (ブリュワーの推測と整合性および可用性を備えたパーティション トレラントな Web サービスの実現可能性)](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)」
4. Daniel Abadi 著: 「[Consistency Tradeoffs in Modern Distributed Database Systems Design (最新の分散データベース システム設計における整合性のトレードオフ)](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)」
5. Martin Kleppmann 著:  「[Please stop calling databases CP or AP (データベースを CP または AP と呼ぶのはやめてください)](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)」
6. Peter Bailis その他共著: 「[Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)」
7. Naor、Wool 共著:  「[The Load, Capacity and Availability of Quorum Systems (クォーラム システムの負荷、容量、および可用性)](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)」
8. Herlihy、Wing 共著:  「[Lineralizability: A Correctness Condition for Concurrent Objects (線形化可能性: 同時実行オブジェクトの正確性の条件)](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)」
9. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
