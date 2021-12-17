---
title: 'クイックスタート: ワンクリックでのデータの取り込みの概要 (プレビュー)'
description: このクイックスタートでは、ワンクリックで Data Explorer プールにデータを取り込む方法について説明します。
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d5fef80a72e210e71eea0331b0d6098d5414d78a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478022"
---
# <a name="quickstart-ingest-data-using-one-click-preview"></a>クイックスタート: ワンクリックでのデータの取り込み (プレビュー)

ワンクリックでのインジェストを使用すると、データ インジェスト プロセスを簡単かつ迅速に、直感的に行うことができます。 ワンクリックでのインジェストは、データの取り込み、データベース テーブルの作成、マッピング構造の準備をすばやく行い、開始するのに役立ちます。 1 回限りまたは継続的なインジェスト プロセスとして、データ形式の異なるさまざまな種類のソースからデータを選択します。

次の機能により、ワンクリックでのインジェストが使いやすくなります。

* インジェスト ウィザードによる直感的なエクスペリエンス
* わずか数分でのデータの取り込み
* ローカル ファイル、BLOB、コンテナーなどの、さまざまな種類のソースからのデータの取り込み (最大 10,000 個の BLOB)
* さまざまな[形式](#file-formats)のデータの取り込み
* 新規または既存のテーブルへのデータの取り込み
* テーブル マッピングとスキーマが推奨されており、簡単に変更できる
<!-- * Continue ingestion easily and quickly from a container with [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion) -->

データの初回取り込み時やデータのスキーマに不慣れな場合に、ワンクリックでのインジェストは特に効果的です。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- テーブルを作成します。[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > テーブルが正常に作成されたことを確認します。 左側のペインで、**[データ]** を選び、*contosodataexplorer* のその他のメニューを選び、**[最新の情報に更新]** を選択します。 *[contosodataexplorer]* で **[テーブル]** を展開し、*StormEvents* テーブルが一覧に表示されていることを確認します。

## <a name="access-the-one-click-wizard"></a>ワンクリック ウィザードにアクセスする

ワンクリックでのインジェスト ウィザードを使用すると、ワンクリックでのデータ取り込み手順を画面の指示に従って行うことができます。

* Azure Synapse からウィザードにアクセスするには、次の手順を行います。

    1. Synapse Studio の左側のペインで、**[データ]** を選択します。
    1. **[Data Explorer データベース]** で、関連するデータベースを右クリックし、**[Azure Data Explorer で開く]** を選択します。

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Azure Synapse Studio のスクリーンショット。特定のプールのコンテキストで Azure Data Explorer を開くところが表示されています。":::

    1. 関連するプールを右クリックし、**[新しいデータを取り込む]** を選択します。

* Azure portal からウィザードにアクセスするには、次の手順を行います。

    1. Azure portal で、関連する Synapse ワークスペースを検索して選択します。
    1. **[Data Explorer プール]** で、関連するプールを選択します。
    1. **[Welcome to Data Explorer pool]\(Data Explorer プールへようこそ\)** ホーム画面で、**[新しいデータを取り込む]** を選択します。

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-portal.png" alt-text="Azure portal のスクリーンショット。特定のプールのコンテキストで Azure Data Explorer を開くところが表示されています。":::


* Azure Data Explorer の Web UI からウィザードにアクセスするには、次の手順を行います。

    1. 開始する前に、次の手順に従って、クエリおよびデータ インジェスト エンドポイントを取得します。
        [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]
    1. Azure Data Explorer の Web UI で、"クエリ エンドポイント" に接続を追加します。
    1. 左側のメニューから **[クエリ]** を選択し、**[データベース]** または **[テーブル]** を右クリックして、**[新しいデータを取り込む]** を選択します。

## <a name="one-click-ingestion-wizard"></a>ワンクリックでのインジェスト ウィザード

> [!NOTE]
> このセクションでは、イベント ハブをデータ ソースとして使用するウィザードについて説明します。 また、これらの手順を使って、BLOB、ファイル、BLOB コンテナー、ADLS Gen2 コンテナーからデータを取り込むこともできます。
>

1. **[宛先]** タブで、取り込まれたデータのデータベースとテーブルを選択します。

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Azure Data Explorer ワンクリック インジェスト ウィザードのスクリーンショット。データベースとテーブルの選択が表示されています。":::

1. **[ソース]** タブで、次のように指定します。
    1. インジェストの [ソースの種類] として **[イベント ハブ]** を選択します。

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-source-type.png" alt-text="Azure Data Explorer ワンクリック インジェスト ウィザードのスクリーンショット。ソースの種類の選択が表示されています。":::

    1. 次の情報を使用して、イベント ハブのデータ接続の詳細を入力します。

        | 設定 | 推奨値 | 説明 |
        |--|--|--|
        | データ接続名 | *ContosoDataConnection* | イベント ハブ データ接続の名前 |
        | サブスクリプション | *Contoso_Synapse* | イベント ハブが存在するサブスクリプション。 |
        | イベント ハブの名前空間 | *contosoeventhubnamespace* | イベント ハブの名前空間。 |
        | コンシューマー グループ | *contosoconsumergroup* | Even Hub コンシューマー グループの名前。 |

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Azure Data Explorer ワンクリック インジェスト ウィザードのスクリーンショット。イベント ハブの接続の詳細が表示されています。":::

    1. **[次へ]** を選択します。

### <a name="schema-mapping"></a>スキーマ マッピング

サービスでは、変更可能なスキーマおよびインジェストのプロパティが自動的に生成されます。 新しいテーブルまたは既存のテーブルのどちらに取り込むかによって、既存のマッピング構造を使用することも、新しいマッピング構造を作成することもできます。

**[スキーマ]** タブで、次の操作を行います。
   1. 自動生成された圧縮の種類を確認します。
   1. [データの形式](#file-formats)を選択します。 異なる形式を使用すると、さらに変更を加えることができます。
   1. [[エディター] ウィンドウ](#editor-window)でマッピングを変更します。

#### <a name="file-formats"></a>ファイル形式

ワンクリック インジェストでは、[インジェスト用に Data Explorer でサポートされているすべてのデータ形式](data-explorer-ingest-data-supported-formats.md)からデータを取り込むことができます。

### <a name="editor-window"></a>エディター ウィンドウ

**[スキーマ]** タブの **[エディター]** ウィンドウで、必要に応じてデータ テーブルの列を調整できます。

テーブルに加えることができる変更は、次のパラメーターによって異なります。

* **テーブル** の種類が新規かまたは既存か
* **マッピング** の種類が新規かまたは既存か

テーブルの種類です。 | マッピングの種類 | 使用可能な調整|
|---|---|---|
|新しいテーブル   | 新しいマッピング |データ型の変更、列名の変更、列の削除、昇順で並べ替え、降順で並べ替え  |
|既存のテーブル  | 新しいマッピング | 新しい列 (その後データ型の変更、名前の変更、および更新が可能)<br> 新しい列、昇順で並べ替え、降順で並べ替え  |
| | 既存のマッピング | 昇順で並べ替え、降順で並べ替え

> [!NOTE]
> 新しい列を追加するとき、または列を更新するときに、マッピング変換を変更できます。 詳細については、[マッピング変換](#mapping-transformations)に関するページを参照してください。

<!-- >[!NOTE]
> At any time, you can open the [command editor](one-click-ingestion-new-table.md#command-editor) above the **Editor** pane. In the command editor, you can view and copy the automatic commands generated from your inputs. -->

#### <a name="mapping-transformations"></a>マッピング変換

一部のデータ形式マッピング (Parquet、JSON、Avro) では、簡単な取り込み時の変換がサポートされています。 マッピング変換を適用するには、[エディター ウィンドウ](#editor-window)で列を作成または更新します。

マッピング変換は、データ型が int または long である **ソース** を使用して、string または datetime **型** の列に対して実行できます。 サポートされているマッピング変換は次のとおりです。

* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

### <a name="data-ingestion"></a>データ インジェスト

スキーマ マッピングと列の操作が完了すると、インジェスト ウィザードでデータ インジェスト プロセスが開始されます。

* **コンテナー以外** のソースからのデータ取り込みは、直ちに結果が反映されます。

* データ ソースが **コンテナー** の場合は、次のようになります。
    * Data Explorer の[バッチ処理ポリシー](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context)によりデータが集計されます。
    * インジェストが完了すると、インジェスト レポートをダウンロードして、処理された各 BLOB のパフォーマンスを確認できます。
    <!-- * You can select **Create continuous ingestion** and set up [continuous ingestion using Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion). -->

### <a name="initial-data-exploration"></a>初期データ探索

インジェストが完了すると、ウィザードには、データの初期探索で **クイック コマンド** を使用するためのオプションが表示されます。

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)
