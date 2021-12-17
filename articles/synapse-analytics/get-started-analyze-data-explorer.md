---
title: 'クイックスタート: Data Explorer プールを使用して分析を開始する (プレビュー)'
description: このクイックスタートでは、Data Explorer を使用してデータを分析する方法について説明します。
ms.topic: quickstart
ms.date: 09/30/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 1473b0db71d0ceaa4e7f78d20b10319047427311
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478443"
---
# <a name="quickstart-analyze-with-data-explorer-preview"></a>クイックスタート: Data Explorer を使用して分析する (プレビュー)

この記事では、Azure Synapse 向けの Data Explorer を使用して、データを読み込んで分析する基本的な手順について説明します。

## <a name="create-a-data-explorer-pool"></a>Data Explorer プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Data Explorer プール]** を選びます。
1. **[新規]** を選び、 **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | Azure Data Explorer プール名 | contosodataexplorer | これは、Azure Data Explorer プールに付けられる名前です。 |
    | ワークロード | コンピューティング最適化 | このワークロードにより、CPU から SSD へのストレージの比率が高くなります。 |
    | ノード サイズ | 小 (4 コア) | このクイックスタートのコストを削減するために、最小サイズに設定します |

    > [!IMPORTANT]
    > Azure Data Explorer プールで使用できる名前には、特定の制限があることに注意してください。 この名前には、4 文字から 15 文字の間で小文字と数字のみを含める必要があります。また、先頭は文字で始める必要があります。

1. **[確認と作成]**  >  **[作成]** の順に選択します。 Azure Data Explorer プールのプロビジョニング プロセスが開始されます。

## <a name="create-a-data-explorer-database"></a>Data Explorer データベースを作成する

1. Synapse Studio の左側のペインで、 **[データ]** を選びます。
1. **&plus;** (新しいリソースの追加) > **[Data Explorer プール]** を選び、次の情報を貼り付けます。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | プール名 | *contosodataexplorer* | 使用する Data Explorer プールの名前 |
    | Name | *TestDatabase* | データベース名はクラスター内で一意である必要があります。 |
    | 既定のリテンション期間 | *365* | クエリにデータを使用できることが保証される期間 (日数) です。 期間は、データが取り込まれた時点から測定されます。 |
    | 既定のキャッシュ期間 | *31* | 頻繁にクエリされるデータが、長期ストレージではなく SSD ストレージまたは RAM で利用できるように保持される期間 (日数) です。 |

1. **[作成]** を選択してデータベースを作成します。 通常、作成にかかる時間は 1 分未満です。

## <a name="ingest-sample-data-and-analyze-with-a-simple-query"></a>サンプル データを取り込み、単純なクエリで分析する

1. Synapse Studio の左側のペインで、 **[開発]** を選びます。
1. **[KQL スクリプト]** で、 **&plus;** (新しいリソースの追加) > **[KQL スクリプト]** を選びます。 右側のペインで、スクリプトに名前を指定できます。
1. **[接続先]** メニューで、 *[contosodataexplorer]* を選びます。
1. **[データベースの使用]** メニューで、 *[TestDatabase]*  を選びます。
1. 次のコマンドを貼り付け、**[実行]** を選択して、StormEvents テーブルを作成します。

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > テーブルが正常に作成されたことを確認します。 左側のペインで、 **[データ]** を選び、*contosodataexplorer* のその他のメニューを選び、 **[最新の情報に更新]** を選びます。 *[contosodataexplorer]* で **[テーブル]** を展開し、*StormEvents* テーブルが一覧に表示されていることを確認します。

1. 次のコマンドを貼り付け、**[実行]** を選択して、StormEvents テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. 取り込みが完了したら、次のクエリに貼り付けて､ウィンドウでクエリを選択し､**[実行]** を選択します。

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

    取り込まれたサンプル データから次のクエリ結果が返されます。

    :::image type="content" source="data-explorer/media/get-started-analyze-data-explorer/sample-query-results.png" alt-text="サンプル データに対して実行されたクエリの結果":::

## <a name="next-steps"></a>次の手順

- [チュートリアル: KQL クエリの使用](/azure/data-explorer/kusto/query/tutorial?context=/azure/synapse-analytics/context/context&pivots=synapse)
- [Data Explorer プールを監視する](data-explorer/data-explorer-monitor-pools.md)
