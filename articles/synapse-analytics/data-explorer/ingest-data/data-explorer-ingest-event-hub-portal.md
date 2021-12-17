---
title: イベント ハブから Azure Synapse Data Explorer にデータを取り込む (プレビュー)
description: イベント ハブから Azure Synapse Data Explorer にデータを取り込む (読み込む) 方法について学習します。
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 54599b7d57b09b0815086231b5cc76cfbffc18b8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720267"
---
# <a name="ingest-data-from-event-hub-into-azure-synapse-data-explorer"></a>イベント ハブから Azure Synapse Data Explorer にデータを取り込む

> [!div class="op_single_selector"]
> * [ポータル](data-explorer-ingest-event-hub-portal.md)
> * [ワンクリック](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager テンプレート](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

Azure Synapse Data Explorer は、ビッグ データ ストリーミング プラットフォームおよびイベント取り込みサービスである Event Hubs からのインジェスト (データの読み込み) を提供します。 [Event Hubs](../../../event-hubs/event-hubs-about.md) は、1 秒あたり数百万件のイベントをほぼリアルタイムで処理できます。 この記事では、イベント ハブを作成し、Azure Synapse Data Explorer からこれに接続し、システム経由でデータ フローを確認します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Event Hubs からデータが送信されるターゲット テーブルを作成します
    1. Synapse Studio の左側のウィンドウで、 **[開発]** を選択します。
    1. **[KQL スクリプト]** で、 **&plus;** (新しいリソースの追加) > **[KQL スクリプト]** を選択します。 右側のウィンドウで、スクリプト名を指定できます。
    1. **[接続先]** メニューで、[*contosodataexplorer*] を選択します。
    1. **[データベースの使用]** メニューで、 *[TestDatabase]* を選択します。
    1. 次のコマンドを貼り付け、 **[実行]** を選択してテーブルを作成します。

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
        ```
    
        > [!TIP]
        > テーブルが正常に作成されたことを確認します。 左側のペインで、 **[データ]** を選択し、*contosodataexplorer* の [その他] メニューを選択し、 **[最新の情報に更新]** を選択します。 *[contosodataexplorer]* で **[テーブル]** を展開し、*TestTable* テーブルが一覧に表示されていることを確認します。

    1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、テーブル (TestTable) の列名とデータ型に受信 JSON データをマップします。
    
        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
        ```

- データ接続 (省略可能) には、[ユーザー割り当てマネージド ID](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity) または[システム割り当てマネージド ID ](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) を使用することをお勧めします。
- データを生成してイベント ハブに送信する[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)。 ご使用のシステムにサンプル アプリをダウンロードしてください。
- サンプル アプリを実行するための [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-an-event-hub"></a>イベント ハブを作成する

Azure portal で Azure Resource Manager テンプレートを使用して、イベント ハブを作成します。

1. イベント ハブを作成するには、次のボタンを使用してデプロイを開始します。 この記事の残りの手順を実行できるよう、右クリックして **[新しいウィンドウで開く]** を選択してください。

    [![[Azure にデプロイ] ボタン](../media/ingest-data-event-hub/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.eventhub%2Fevent-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    **[Azure へのデプロイ]** ボタンをクリックすると、Azure portal に移動します。

    ![イベント ハブの作成フォーム](../media/ingest-data-event-hub/deploy-to-azure.png)

1. イベント ハブを作成するサブスクリプションを選択し、*test-hub-rg* というリソース グループを作成します。

    ![リソース グループを作成する](../media/ingest-data-event-hub/create-resource-group.png)

1. フォームに次の情報を入力します。

    次の表に記載されていない設定については、既定値のままにしてください。

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | イベント ハブに使用する Azure サブスクリプションを選択します。|
    | Resource group | *test-hub-rg* | 新しいリソース グループを作成します。 |
    | 場所 | *米国西部* | この記事では *[米国西部]* を選択します。 運用システムでは、ニーズに最も適したリージョンを選択します。 最適なパフォーマンスを得るには、Azure Synapse Data Explorer クラスターと同じ場所にイベント ハブの名前空間を作成します (高スループットのイベント ハブの名前空間に最も重要です)。
    | 名前空間名 | 一意の名前空間名 | 名前空間を識別する一意の名前を選択します。 たとえば、*mytestnamespace* と指定します。 指定した名前にドメイン名 *servicebus.windows.net* が付加されます。 この名前には、文字、数字、ハイフンのみを含めることができます。 名前の先頭は英字、末尾は英字または数字にする必要があります。 値の長さは 6 から 50 文字にする必要があります。
    | イベント ハブ名 | *test-hub* | イベント ハブは、固有のスコープ コンテナーを提供する名前空間の下にあります。 イベント ハブ名は、名前空間内で一意にする必要があります。 |
    | コンシューマー グループ名 | *test-group* | コンシューマー グループを使用すると、複数の使用アプリケーションがそれぞれイベント ストリーム ビューを持つことができるようになります。 |
    | | |

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 作成されたリソースの **概要** を確認します。 **[作成]** を選択して、サブスクリプション内にリソースを作成することを確認します。

    :::image type="content" source="../media/ingest-data-event-hub/review-create.png" alt-text="イベント ハブの名前空間、イベント ハブ、およびコンシューマー グループの確認と作成を行うための、Azure portal のスクリーンショット。":::

1. プロビジョニング プロセスを監視するには、ツール バーの **[通知]** を選択します。 デプロイが完了するまでには数分かかることがありますが、すぐに次の手順に進むこともできます。

    ![[通知] アイコン](../media/ingest-data-event-hub/notifications.png)

### <a name="authentication-considerations"></a>認証に関する考慮事項

イベント ハブでの認証に使用する ID の種類によっては、追加の構成が必要な場合があります。

- ユーザー割り当てマネージド ID を使用してイベント ハブで認証を行う場合は、お使いのイベント ハブ > **[ネットワーク]** に移動し、 **[Allow access from]\(アクセスを許可する\)** で **[すべてのネットワーク]** を選択し、変更を保存します。

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-all-networks.png" alt-text="イベント ハブの [ネットワーク] ページのスクリーンショット。すべてのネットワークへのアクセスを許可する選択が示されています。":::

- システム割り当てマネージド ID を使用してイベント ハブで認証を行う場合は、お使いのイベント ハブ > **[ネットワーク]** に移動し、 **[アクセスを許可する]** ですべてのネットワークからのアクセスを許可するか、 **[選択されたネットワーク]** を選択して **[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可しますか?]** を選択し、変更を保存します。

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-trusted-services.png" alt-text="イベント ハブの [ネットワーク] ページのスクリーンショット。信頼されたサービスへのアクセスを許可する選択が示されています。":::

## <a name="connect-to-the-event-hub"></a>イベント ハブへの接続

ここで、Data Explorer プールからイベント ハブに接続します。 この接続が確立されると、イベント ハブに送られてくるデータが、この記事の前の方で作成したテスト テーブルにストリームとして送られます。

1. ツールバーの **[通知]** を選択して、イベント ハブのデプロイが成功したことを確認します。

1. 作成した Data Explorer プールで、 **[データベース]**  >  **[TestDatabase]** を選択します。

    :::image type="content" source="../media/ingest-data-event-hub/select-test-database.png" alt-text="テスト データベースの選択。":::

1. **[データ接続]** 、 **[データ接続の追加]** の順に選択します。

    :::image type="content" source="../media/ingest-data-event-hub/event-hub-connection.png" alt-text="[データ インジェスト]、[データ接続の追加] の順に選択します。":::

### <a name="create-a-data-connection-preview"></a>データ接続を作成する (プレビュー)

フォームに次の情報を入力し、 **[作成]** を選択します。

:::image type="content" source="../media/ingest-data-event-hub/data-connection-pane.png" alt-text="[データ接続] ペイン。イベント ハブ - Azure Synapse Data Explorer。":::

**設定** | **推奨値** | **フィールドの説明**
|---|---|---|
| データ接続名 | *test-hub-connection* | Azure Synapse Data Explorer で作成する接続の名前。|
| サブスクリプション |      | イベントハブ リソースが配置されているサブスクリプション ID。 このフィールドは自動入力されます。 |
| Event Hub 名前空間 | 一意の名前空間名 | 以前に選択した、名前空間を識別する名前。 |
| イベント ハブ | *test-hub* | 作成したイベント ハブ。 |
| コンシューマー グループ | *test-group* | 作成したイベント ハブに定義されているコンシューマー グループ。 |
| イベント システム プロパティ | 関連するプロパティを選択する | [イベント ハブのシステム プロパティ](../../../service-bus-messaging/service-bus-amqp-protocol-guide.md#message-annotations)。 1 つのイベント メッセージに複数のレコードがある場合、システム プロパティは最初のレコードに追加されます。 システム プロパティを追加する場合は、テーブル スキーマと[マッピング](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)を[作成](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context)または[更新](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context)して、選択したプロパティを含めます。 |
| 圧縮 | *なし* | イベント ハブ メッセージ ペイロードの圧縮の種類。 サポートされている圧縮の種類は、"*なし、Gzip*" です。|
| マネージド ID | システム割り当て | イベント ハブに対する読み取りアクセスのために Data Explorer クラスターによって使用されるマネージド ID。<br /><br />**注**:<br />データ接続が作成されると、次のようになります。<br/>\- "*システム割り当て*" ID が存在しない場合は自動的に作成されます<br />\- マネージド ID は、"*Azure Event Hubs データ受信者*" ロールが自動的に割り当てられ、Data Explorer クラスターに追加されます。 ロールが割り当てられ、ID がクラスターに追加されたことを確認することをお勧めします。 |

#### <a name="target-table"></a>ターゲット テーブル

挿入したデータをルーティングするには、"*静的*" と "*動的*" という 2 つのオプションがあります。
この記事では、静的ルーティングを使用し、テーブル名、データ形式、およびマッピングを既定値として指定します。 イベントハブ メッセージにデータ ルーティング情報が含まれている場合、このルーティング情報によって既定の設定が上書きされます。

1. 次のルーティング設定を入力します。

    :::image type="content" source="../media/ingest-data-event-hub/default-routing-settings.png" alt-text="イベント ハブにデータを取り込むための既定のルーティング設定 - Azure Synapse Data Explorer。":::

    |**設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル名 | *TestTable* | **TestDatabase** に作成したテーブル。 |
    | データ形式 | *JSON* | サポートされている形式は、Avro、CSV、JSON、MULTILINE JSON、ORC、PARQUET、PSV、SCSV、SOHSV、TSV、TXT、TSVE、APACHEAVRO、および W3CLOG です。 |
    | マッピング | *TestMapping* | **TestDatabase** に作成した [マッピング](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)。これにより、受信データを **TestTable** の列名とデータ型にマッピングします。 JSON、MULTILINE JSON、AVRO では必須。その他の形式では省略可能。|

    > [!NOTE]
    >
    > * **既定のルーティング設定** をすべて指定する必要はありません。 部分的な設定も受け入れられます。
    > * データ接続の作成後にエンキューされたイベントのみが取り込まれたます。

1. **［作成］** を選択します

### <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブル スキーマとマッピングに [システム プロパティ](data-explorer-ingest-event-hub-overview.md#system-properties)を含める必要があります。

## <a name="copy-the-connection-string"></a>接続文字列のコピー

前提条件の一覧にある[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)を実行する場合は、イベント ハブの名前空間用の接続文字列が必要です。

1. 作成したイベント ハブ名前空間で、 **[共有アクセス ポリシー]** 、 **[RootManageSharedAccessKey]** の順に選択します。

    ![共有アクセス ポリシー。](../media/ingest-data-event-hub/shared-access-policies.png)

1. **[接続文字列 - 主キー]** をコピーします これを、次のセクションで貼り付けます。

    ![接続文字列:](../media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>サンプル データを作成する

ダウンロードした[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)を使用してデータを生成します。

1. Visual Studio でサンプル アプリ ソリューションを開きます。
1. *program.cs* ファイルで、`eventHubName` 定数をイベント ハブの名前に更新し、`connectionString` 定数をイベント ハブ名前空間からコピーした接続文字列に更新します。

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. アプリケーションをビルドし、実行します。 アプリからイベント ハブにメッセージが送信され、10 秒ごとに状態が出力されます。
1. アプリからいくつかメッセージが送信されたら、次の手順に進み、イベント ハブとテスト テーブルへのデータの流れを確認します。

## <a name="review-the-data-flow"></a>データ フローの確認

データを生成するアプリを使用して、そのデータがイベント ハブからクラスター内のテーブルに送信されるのを確認できるようになりました。

1. アプリの実行中に Azure portal でイベント ハブを確認すると、アクティビティの急上昇が見られます。

    ![イベント ハブ グラフ。](../media/ingest-data-event-hub/event-hub-graph.png)

1. これまでにデータベースに届いたメッセージ数を確認するには、テスト データベースで次のクエリを実行します。

    ```Kusto
    TestTable
    | count
    ```

1. メッセージの内容を表示するには、次のクエリを実行します。

    ```Kusto
    TestTable
    ```

    結果セットは次の図のようになります。

    ![メッセージの結果セット。](../media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    >
    > * Azure Synapse Data Explorer には、インジェスト プロセスを最適化することを目的とした、データ インジェストの集計 (バッチ処理) ポリシーがあります。 既定のバッチ処理ポリシーは、バッチについて次の条件のいずれかが真になると、バッチを封印するように構成されています。最大遅延時間 5 分、合計サイズ 1 G、または 1000 個の BLOB。 そのため、待機時間が発生する可能性があります。 詳細については、[バッチ処理ポリシー](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context)に関するページを参照してください。
    > * イベント ハブ インジェストには、10 秒または 1 MB のイベント ハブの応答時間が含まれます。
    > * 応答時間の遅れを減らすには、ストリーミングをサポートするようにテーブルを構成します。 [ストリーミング ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このイベント ハブを今後使用する予定がない場合は、コストが発生しないように **test-hub-rg** をクリーンアップします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。

    左側のメニューが折りたたまれている場合は、 ![展開ボタン。](../media/ingest-data-event-hub/expand.png) をクリックして展開します。

   ![削除するリソース グループの選択。](../media/ingest-data-event-hub/delete-resources-select.png)

1. **test-resource-group** で **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前 (*test-hub-rg*) を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)