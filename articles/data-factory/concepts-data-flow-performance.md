---
title: Azure Data Factory の Mapping Data Flow のパフォーマンスとチューニング ガイド | Microsoft Docs
description: Mapping Data Flow を使用する場合に、Azure Data Factory でのデータ フローのパフォーマンスに影響する主な要因について説明します。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172442"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapping Data Flow のパフォーマンスとチューニング ガイド

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory の Mapping Data Flow では、大規模なデータ変換の設計、デプロイ、および調整するためのコード不要のブラウザー インターフェイスが提供されます。

> [!NOTE]
> ADF Mapping Data Flow 全般に慣れていない場合は、この記事を読む前に、[データ フローの概要](concepts-data-flow-overview.md)に関するページをご覧ください。
>

> [!NOTE]
> ADF UI から Data Flow の設計およびテストしている場合は、クラスターのウォーム アップを待機することなく、リアルタイムでデータ フローを実行できるように、必ず [デバッグ] スイッチをオンにします。
>

![[デバッグ] ボタン](media/data-flow/debugb1.png "デバッグ")

## <a name="optimizing-for-azure-sql-database"></a>Azure SQL Database の最適化

![ソース部分](media/data-flow/sourcepart2.png "ソース部分")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>ソース変換のデータベース テーブルの列キーに基づいて、Spark データのパーティション分割をご使用のソース データベースのパーティション分割と一致させることができます。

* [最適化] に移動し、[ソース] を選択します。 クエリで特定のテーブル列または型のいずれかを設定します。
* [列] を選択した場合は、パーティション列を選択します。
* また、Azure SQL DB への最大接続数も設定します。 データベースへの並列接続を取得するために高い数値の設定を試すことができます。 ただし、場合によっては、接続の数を制限することでパフォーマンスが高速になることもあります。

### <a name="set-batch-size-and-query-on-source"></a>ソースでバッチ サイズとクエリを設定する

![ソース](media/data-flow/source4.png "ソース")

* バッチ サイズを設定することで、行単位ではなくメモリにセットでデータを格納するように ADF に指示します。 これは省略可能な設定で、適切にサイズを設定しないと、コンピューティング ノード上のリソースが不足する場合があります。
* クエリを設定することで、処理のために Data Flow に到着するよりも前に、ソースで直接行をフィルターできます。これにより初期データの取得を高速化することができます。
* クエリを使用する場合は、Azure SQL DB、つまり READ UNCOMMITTED に省略可能なクエリ ヒントを追加することができます。

### <a name="set-sink-batch-size"></a>シンクのバッチ サイズを設定する

![シンク](media/data-flow/sink4.png "シンク")

* データ フローの行単位の処理を回避するためには、Azure SQL DB のシンクの設定で [バッチ サイズ] を設定します。 これにより、指定したサイズに基づいてデータベースの書き込みをバッチで処理するように ADF に指示します。

### <a name="set-partitioning-options-on-your-sink"></a>シンクでパーティション分割オプションを設定する

* ターゲットの Azure SQL DB テーブルのデータをパーティション分割していない場合でも、[最適化] タブに移動して、パーティション分割を設定します。
* 1 つのノード/パーティションからすべての接続を強制する代わりに、ADF に Spark 実行クラスター上でラウンド ロビン パーティション分割を使用するように指示するだけで、多くの場合、データの読み込みがはるかに高速になります。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Azure Integration Runtime でコンピューティング エンジンのサイズを増やす

![新しい IR](media/data-flow/ir-new.png "新しい IR")

* コアの数を増やすと、ノードの数も増えるため、クエリの実行および Azure SQL DB への書き込みの処理能力が向上します。
* ご利用のコンピューティング ノードにさらに多くのリソースを適用するには、[コンピューティング最適化] および [メモリ最適化] のオプションをお試しください。

### <a name="disable-indexes-on-write"></a>書き込み時にインデックスを無効にする
* シンクからの書き込み先のターゲット テーブルでインデックスを無効にする Data Flow アクティビティの前に、ADF パイプラインのストアド プロシージャ アクティビティを使用します。
* Data Flow アクティビティの後に、これらのインデックスを有効にする別のストアド プロシージャ アクティビティを追加します。

### <a name="increase-the-size-of-your-azure-sql-db"></a>Azure SQL DB のサイズを増やす
* DTU の制限に達したら、ソースおよびシンク Azure SQL DB のサイズ変更をスケジュールしてから、パイプラインを実行してスループットを増やし、Azure スロットルを最小化します。
* パイプラインの実行が完了したら、データベースのサイズを変更して通常のラン レートに戻すことができます。

## <a name="next-steps"></a>次の手順
他の Data Flow の記事を参照します。

- [Data Flow の概要](concepts-data-flow-overview.md)
- [Data Flow のアクティビティ](control-flow-execute-data-flow-activity.md)

