---
title: 'クイックスタート: パイプラインを使用したデータの取り込の概要'
description: このクイックスタートでは、Azure Synapse Pipelines を使用して Data Explorer プールにデータを取り込む方法について説明します。
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 746f37fae42b117853d2f808d9fc5ff9fa9f980b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478020"
---
# <a name="quickstart-ingest-data-using-azure-synapse-pipelines-preview"></a>クイックスタート: Azure Synapse Pipelines を使用してデータを取り込む (プレビュー)

このクイックスタートでは、データソースから Azure Synapse Data Explorer プールにデータを読み込む方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- テーブルを作成します。[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > テーブルが正常に作成されたことを確認します。 左側のペインで、 **[データ]** を選び、*contosodataexplorer* のその他のメニューを選び、 **[最新の情報に更新]** を選択します。 *[contosodataexplorer]* で **[テーブル]** を展開し、*StormEvents* テーブルが一覧に表示されていることを確認します。

- クエリとデータ インジェストのエンドポイントを取得します。 リンクされたサービスを構成するには、クエリ エンドポイントが必要です。
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

Azure Synapse Analytics で、リンクされたサービスとは、他のサービスへの接続情報を定義した場所です。 このセクションでは、Azure Data Explorer のリンク サービスを作成します。

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[リンクされたサービス]** の順に選択します。
1. **[&plus; 新規]** を選択します。

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-linked-service.png" alt-text="[リンクされたサービス] 画面のスクリーンショット。既存のサービスの一覧が表示され、[新規追加] ボタンが強調表示されています。":::

1. ギャラリーから **[Azure Data Explorer]** サービスを選択し、 **[続行]** を選択します。

    :::image type="content" source="../media/ingest-data-pipeline/select-new-data-explorer-linked-service.png" alt-text="[New Linked service]\(新しくリンクされたサービス\) ウィンドウのスクリーンショット。利用可能なサービスの一覧が表示され、新たに追加される Azure Data Explorer が強調表示されています。":::

1. [New Linked service]\(新しくリンクされたサービス\) ページで、次の情報を使用します。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | Name | *contosodataexplorerlinkedservice* | 新しい Azure Data Explorer のリンクされたサービスの名前。 |
    | 認証方法 | *Managed Identity* | 新しいサービスの認証方法。 |
    | Account selection method (アカウントの選択方法) | *"手動で入力"* | クエリ エンドポイントを指定するメソッド。 |
    | エンドポイント | *https:\/\/contosodataexplorer.contosoanalytics.dev.kusto.windows.net* | [以前にメモ](#prerequisites)したクエリ エンドポイント。 |
    | データベース | *TestDatabase* | データを取り込むデータベース。 |

    :::image type="content" source="../media/ingest-data-pipeline/create-new-data-explorer-linked-service.png" alt-text="新しくリンクされたサービスの詳細ウィンドウのスクリーンショット。新しいサービスについての入力が必要なフィールドが表示されています。":::

1. **[テスト接続]** を選択して設定を検証し、 **[作成]** を選択します。

## <a name="create-a-pipeline-to-ingest-data"></a>データを取り込むパイプラインを作成する

パイプラインには、一連のアクティビティを実行するための論理フローが含まれています。 このセクションでは、優先ソースのデータを Data Explorer プールに取り込むコピー アクティビティを含むパイプラインを作成します。

1. Synapse Studio の左側のペインで、 **[統合]** を選択します。

1. **[&plus;]**  >  **[パイプライン]** の順に選択します。 右側のペインで、パイプラインに名前を付けることができます。

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-pipeline.png" alt-text="新しいパイプラインを作成するための選択内容を示すスクリーンショット。":::

1. **[アクティビティ]**  >  **[移動と変換]** で、 **[データ コピー]** をパイプライン キャンバスにドラッグします。
1. コピー アクティビティを選択し、 **[ソース]** タブにアクセスします。データのコピー元として、新しいソース データセットを選択または作成します。
1. **[シンク]** タブに移動します。 **[新規]** を選択して、新しいシンク データセットを作成します。

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink.png" alt-text="パイプラインのコピー アクティビティのスクリーンショット。新しいシンクを作成するための選択が表示されています。":::

1. ギャラリーから **[Azure Data Explorer]** データセットを選択し、 **[続行]** を選択します。
1. **[プロパティの設定]** ウィンドウで、次の情報を使用してから **[OK]** を選択します。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | Name | *AzureDataExplorerTable* | 新しいパイプラインの名前。 |
    | リンクされたサービス | *contosodataexplorerlinkedservice* | 前に作成した、リンクされたサービス。 |
    | テーブル | *StormEvents* | 前に作成したテーブル。 |

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink-set-properties.png" alt-text="パイプラインのコピー アクティビティ セットの [プロパティ] ペインのスクリーンショット。新しいシンクのために入力する必要があるフィールドが表示されています。":::

1. パイプラインを検証するには、ツール バーの **[検証]** を選択します。 パイプラインの検証の出力結果がページの右側に表示されます。

## <a name="debug-and-publish-the-pipeline"></a>パイプラインをデバッグして発行する

パイプラインの構成が完了したら、成果物を発行する前にデバッグを実行することで、すべてが正しいことを確認できます。

1. ツールバーの **[デバッグ]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。

1. パイプラインの実行に成功したら、上部のツール バーで **[すべて発行]** を選択します。 この操作により、作成したエンティティ (データセットとパイプライン) が Synapse Analytics サービスに発行されます。
1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、右上にあるベル ボタンを選択します。

## <a name="trigger-and-monitor-the-pipeline"></a>パイプラインをトリガーして監視する

このセクションでは、前の手順で発行したパイプラインを手動でトリガーします。

1. ツール バーの **[トリガーの追加]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[OK]** を選択します。

1. 左側のサイドバーにある **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。
1. パイプラインの実行が正常に完了したら、 **[パイプライン名]** 列のリンクを選択してアクティビティの実行の詳細を表示するか、またはパイプラインを再実行します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。
1. コピー操作の詳細を確認するには、 **[アクティビティ名]** 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。
1. パイプラインの実行ビューに戻るには、上部の **[すべてのパイプラインの実行]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。
1. データが Data Explorer プールに正しく書き込まれていることを確認します。

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)
