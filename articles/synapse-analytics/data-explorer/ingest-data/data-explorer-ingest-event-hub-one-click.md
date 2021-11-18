---
title: ワンクリック インジェストを使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (プレビュー)
description: ワンクリックを使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (読み込む) 方法について学習します。
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 6bfb614f3f0263b6ceff3fbf1e00a6c90215c6a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720209"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-synapse-data-explorer-preview"></a>ワンクリック インジェストを使用して Azure Synapse Data Explorer 用にイベント ハブ データ接続を作成する (プレビュー)

> [!div class="op_single_selector"]
> * [ポータル](data-explorer-ingest-event-hub-portal.md)
> * [ワンクリック](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager テンプレート](data-explorer-ingest-event-hub-resource-manager.md)

Azure Synapse Data Explorer には、ビッグ データ ストリーミング プラットフォームおよびイベント取り込みサービスである Event Hubs からの取り込み (データの読み込み) 機能があります。 [Event Hubs](../../../event-hubs/event-hubs-about.md) は、1 秒あたり数百万件のイベントをほぼリアルタイムで処理できます。 この記事では、[ワンクリック インジェスト](data-explorer-ingest-data-one-click.md) エクスペリエンスを使用して、Azure Synapse Data Explorer のテーブルにイベント ハブを接続します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [取り込み用のデータが含まれるイベント ハブ](data-explorer-ingest-event-hub-portal.md#create-an-event-hub)。

> [!NOTE]
> Synapse ワークスペースでデータ流出防止が有効になっているマネージド仮想ネットワークを使用している場合、イベント ハブから Data Explorer プールへのデータの取り込みは機能しません。

## <a name="ingest-new-data"></a>新しいデータを取り込む

1. Synapse Studio の左側のウィンドウで、 **[データ]** を選択します。

1. **[Data Explorer データベース]** で、関連するデータベースを右クリックしてから、 **[Azure Data Explorer で開く]** を選択します。

    :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="特定のプールのコンテキストで Azure Data Explorer を開いているところを示す Azure Synapse Studio のスクリーンショット。":::

1. Web UI の左側のメニューで、 **[データ]** タブを選択します。 

    :::image type="content" source="../media/ingest-data-event-hub/one-click-ingestion-event-hub.png" alt-text="Web UI で Event Hub からのデータのワンクリック インジェストを選択する。":::

1. **[イベント ハブからのデータの取り込み]** カードで、 **[取り込み]** を選択します。 

**[新しいデータの取り込み]** ウィンドウが、 **[配信先]** タブが選択された状態で開きます。

### <a name="destination-tab"></a>[配信先] タブ

:::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="[配信先] タブのスクリーン ショット。次の [ソース] に進む前に、[クラスター]、[データベース]、[テーブル] の各フィールドを入力する必要がある。":::

1. **[クラスター]** と **[データベース]** の各フィールドには、自動的に値が設定されます。 ドロップダウン メニューから別のクラスターまたはデータベースを選択できます。

1. **[テーブル]** の下で **[新しいテーブルを作成する]** を選択し、新しいテーブルの名前を入力します。 または、既存のテーブルを使用します。 

    > [!NOTE]
    > テーブル名は、1 から 1024 文字にする必要があります。 英数字、ハイフン、アンダースコアを使用できます。 特殊文字はサポートされていません。

1. **[Next: Source]\(次へ: ソース\)** を選択します。

### <a name="source-tab"></a>[ソース] タブ

1. **[ソースの種類]** で、 **[イベント ハブ]** を選択します。 

1. **[データ接続]** で、次のフィールドに入力します。

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="プロジェクト詳細のフィールドが入力された [ソース] タブのスクリーンショット - ワンクリック エクスペリエンスでのイベント ハブを使用した Azure Synapse Data Explorer への新しいデータの取り込み。":::

    |**設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | データ接続名 | *ContosoDataConnection*  | データ接続を識別する名前。
    | サブスクリプション |      | イベントハブ リソースが配置されているサブスクリプション ID。  |
    | Event Hub 名前空間 |  | 名前空間を識別する名前。 |
    | イベント ハブ |  | 使用するイベント ハブ。 |
    | コンシューマー グループ |  | イベント ハブに定義されているコンシューマー グループ。 |
    | イベント システム プロパティ | 関連するプロパティを選択する | [イベント ハブのシステム プロパティ](../../../service-bus-messaging/service-bus-amqp-protocol-guide.md#message-annotations)。 1 つのイベント メッセージに複数のレコードがある場合、システム プロパティは最初のものに追加されます。 システム プロパティを追加する場合は、テーブル スキーマと[マッピング](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)を[作成](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context)または[更新](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context)して、選択したプロパティを含めます。 |

1. **[Next: Schema]\(次へ: スキーマ\)** を選択します。

## <a name="schema-tab"></a>スキーマ タブ

データは [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトの形式でイベント ハブから読み取られます。 サポートされている形式は、CSV、JSON、PSV、SCsv、SOHsv、TSV、TXT、TSVE です。

<!-- For information on schema mapping with JSON-formatted data, see [Edit the schema](one-click-ingestion-existing-table.md#edit-the-schema).
For information on schema mapping with CSV-formatted data, see [Edit the schema](one-click-ingestion-new-table.md#edit-the-schema). -->

:::image type="content" source="../media/ingest-data-event-hub/schema-tab.png" alt-text="ワンクリック エクスペリエンスでのイベント ハブを使用した Azure Synapse Data Explorer への新しいデータの取り込みにおける [スキーマ] タブのスクリーンショット。":::

1. プレビュー ウィンドウに表示されるデータが完全でない場合は、必要なすべてのデータ フィールドを含むテーブルを作成するために、さらにデータが必要になることがあります。 次のコマンドを使用して、イベント ハブから新しいデータをフェッチします。
    * **Discard and fetch new data (破棄して新しいデータをフェッチする)** : 表示されたデータを破棄し、新しいイベントを検索します。
    * **Fetch more data (さらにデータをフェッチする)** :既に見つかったイベントに加えて、さらにイベントを検索します。 
    
    > [!NOTE]
    > データのプレビューを表示するには、イベント ハブがイベントを送信している必要があります。
        
1. **概要** を選択します。

## <a name="continuous-ingestion-from-event-hub"></a>イベント ハブからの継続的なインジェスト

**[Continuous ingestion from Event Hub established]\(イベント ハブからの継続的なインジェストが確立\)** ウィンドウでは、確立が正常に完了すると、ステップすべてに緑色のチェックマークが表示されます。 これらの手順の下にあるカードでは、 **[Quick queries]\(クイック クエリ\)** を使用してデータを探索したり、 **[ツール]** を使用して行った変更を元に戻したり、イベント ハブの接続とデータを **監視** したりするためのオプションが提供されます。

:::image type="content" source="../media/ingest-data-event-hub/data-ingestion-completed.png" alt-text="ワンクリック エクスペリエンスを使用したイベント ハブから Azure Synapse Data Explorer への取り込みにおける最後の画面のスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)