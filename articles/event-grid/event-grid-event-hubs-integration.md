---
title: Azure Event Grid と Event Hubs 統合
description: Azure Event Grid と Event Hubs を使用して、SQL Data Warehouse にデータを移行する方法について説明します
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 60857327685fca9a5f97588ab51909ce2537d68f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>ビッグ データをデータ ウェアハウスにストリーミングする

Azure [Event Grid](overview.md) は、アプリやサービスからの通知に対応するための、インテリジェントなイベント ルーティング サービスです。 [Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)では、Azure Event Hubs Capture と Azure Event Grid を使用して、イベント ハブから SQL Data Warehouse へデータをシームレスに移行する方法を説明しています。

![アプリケーションの概要](media/event-grid-event-hubs-integration/overview.png)

イベント ハブにデータを送信すると、Capture によりストリームからデータがプルされ、Avro 形式のデータを含むストレージ BLOB が生成されます。 Capture が BLOB を生成すると、イベントがトリガーされます。 Event Grid により、イベントに関するデータがサブスクライバーに配信されます。 ここでは、イベント データは Azure Functions エンドポイントに送信されます。 イベント データには、生成された BLOB のパスが含まれます。 関数がその URL を使用してファイルを取得し、データ ウェアハウスに送信します。

この記事では、次の内容について説明します。

* 次のようなインフラストラクチャをデプロイする
  * Capture が有効なイベント ハブ
  * Capture からのファイル用のストレージ アカウント
  * 関数アプリをホストするための Azure App Service プラン
  * イベントを処理するための関数アプリ
  * データ ウェアハウスをホストするための SQL Server
  * 移行後のデータを格納するための SQL Data Warehouse
* データ ウェアハウスのテーブルを作成する
* 関数アプリにコードを追加する
* イベントをサブスクライブする
* イベント ハブにデータを送信するアプリを実行する
* データ ウェアハウスに移行されたデータを表示する

## <a name="about-the-event-data"></a>イベント データについて

Event Grid により、イベント データがサブスクライバーに配信されます。 Capture ファイルを作成するためのイベント データを次に示します。 特に `data` オブジェクトの `fileUrl` プロパティに注意してください。 関数アプリでこの値を取得し、その値を使用して Capture ファイルを取得します。

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* [Visual Studio 2017 バージョン 15.3.2 以上](https://www.visualstudio.com/vs/)で次のワークロードを使用: .NET デスクトップ開発、Azure 開発、ASP.NET および Web 開発、Node.js 開発、Python 開発。
* お使いのコンピューターにダウンロードされた [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。

## <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする

この記事の内容を簡略化するために、Resource Manager テンプレートを使用して必要なインフラストラクチャをデプロイします。 デプロイされるリソースを確認するには、[テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)をご覧ください。 リソース グループの場所には[サポートされているリージョン](overview.md)を使います。

Azure CLI では、次を使用します。

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

入力を求められたら、パスワードを入力します。

## <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse でテーブルを作成する

[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) スクリプトを実行して、データ ウェアハウスにテーブルを追加します。 スクリプトを実行するには、ポータルで Visual Studio またはクエリ エディターを使用します。

実行するスクリプトは次のとおりです。

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Azure Functions アプリを発行する

1. [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)を Visual Studio 2017 (15.3.2 以上) で開きます。

1. ソリューション エクスプローラーで、**[FunctionEGDWDumper]** を右クリックし、**[発行]** を選択します。

   ![関数アプリの発行](media/event-grid-event-hubs-integration/publish-function-app.png)

1. **[Azure 関数アプリ]** を選択して、**[既存のものを選択]** を選択します。 **[発行]** を選択します。

   ![対象の関数アプリ](media/event-grid-event-hubs-integration/pick-target.png)

1. テンプレートを使ってデプロイした関数アプリを選択します。 **[OK]** を選択します。

   ![関数アプリの選択](media/event-grid-event-hubs-integration/select-function-app.png)

1. Visual Studio でプロファイルを構成している場合は、**[発行]** を選択します。

   ![発行の選択](media/event-grid-event-hubs-integration/select-publish.png)

関数を発行すれば、イベントをサブスクライブする準備が整います。

## <a name="subscribe-to-the-event"></a>イベントをサブスクライブする

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 リソース グループおよび関数アプリを選択します。

   ![関数アプリの表示](media/event-grid-event-hubs-integration/view-function-app.png)

1. 関数を選択します。

   ![関数の選択](media/event-grid-event-hubs-integration/select-function.png)

1. **Event Grid サブスクリプションの追加**を選択します。

   ![[サブスクリプションの追加]](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Event Grid サブスクリプションに名前をつけます。 **Event Hubs の名前空間** をイベントの種類として使用します。 Event Hubs 名前空間のインスタンスを選択する値を提供します。 指定された値としてサブスクライバーのエンドポイントのままにします。 **[作成]** を選択します。

   ![サブスクリプションの作成](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>データを生成するアプリを実行する

イベント ハブ、SQL Data Warehouse、Azure 関数アプリ、イベント サブスクリプションのセットアップが完了しました。 イベント ハブからデータ ウェアハウスにデータを移行する準備が整いました。 イベント ハブのデータを生成するアプリケーションを実行する前に、いくつかの値を構成する必要があります。

1. ポータルで、イベント ハブの名前空間を選択します。 **[接続文字列]** を選択します。

   ![接続文字列の選択](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. **[RootManageSharedAccessKey]** を選択します。

   ![キーの選択](media/event-grid-event-hubs-integration/show-root-key.png)

3. **[Connection string - primary Key]\(接続文字列 – 主キー\)** をコピーします

   ![キーのコピー](media/event-grid-event-hubs-integration/copy-key.png)

4. Visual Studio プロジェクトに戻ります。 WindTurbineDataGenerator プロジェクトで、**program.cs** を開きます。

5. 2 つの定数の値を置き換えます。 **EventHubConnectionString** には、コピーした値を使用します。 **hubdatamigration**イベント ハブの名前を使用します。

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. ソリューションをビルドします。 WindTurbineGenerator.exe アプリケーションを実行します。 数分後に、データ ウェアハウスのテーブルにクエリを実行し、移行されたデータを確認します。

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Hubs Capture の概要については、「[Azure Portal を使用して Event Hubs Capture を有効にする](../event-hubs/event-hubs-capture-enable-through-portal.md)」をご覧ください。
* サンプルの設定と実行に関する詳細については、[Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)に関する記事をご覧ください。