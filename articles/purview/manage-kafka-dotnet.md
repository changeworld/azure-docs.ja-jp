---
title: .NET から Event Hubs を使用して Azure Purview の Atlas Kafka トピックにメッセージを発行したり、トピックからのメッセージを処理したりする
description: この記事では、最新の Azure.Messaging.EventHubs パッケージを使用して、Purview の Apache Atlas Kafka トピックとの間でイベントを送受信する .NET Core アプリケーションを作成する方法について説明します。
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 09/27/2021
ms.openlocfilehash: 670e7425c8b56732a32e1deb0b664bc6544eda0c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209625"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>.NET から Event Hubs を使用して Azure Purview の Atlas Kafka トピックにメッセージを発行したり、トピックからのメッセージを処理したりする 
このクイックスタートでは、**Azure.Messaging.EventHubs** .NET ライブラリを使用して、Azure Purview の Atlas Kafka トピックとの間でイベントを送受信する方法について説明します。 

> [!IMPORTANT]
> マネージド イベント ハブは、Purview アカウントを作成する過程で作成します。[Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。 イベント ハブの Kafka トピック ATLAS_HOOK にメッセージを発行すると、そのメッセージを Purview が取り込んで処理します。 Purview は、イベント ハブの Kafka トピック ATLAS_ENTITIES にエンティティの変更を通知し、ユーザーは、それを取り込んで処理することができます。このクイックスタートでは、新しい **Azure.Messaging.EventHubs** ライブラリを使用します。 


## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に [Event Hubs の概要](../event-hubs/event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- **Microsoft Visual Studio 2019**。 Azure Event Hubs クライアント ライブラリでは、C# 8.0 で導入された新機能を利用しています。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、[.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 以降および[言語バージョン](/dotnet/csharp/language-reference/configure-language-version#override-a-default) を `latest` に設定して、コンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。 無料の Community エディションを含む Visual Studio 2019 は、[こちら](https://visualstudio.microsoft.com/vs/)からダウンロードできます。

## <a name="publish-messages-to-purview"></a>Purview にメッセージを発行する 
このセクションでは、イベント ハブの Kafka トピック **ATLAS_HOOK** を介して Purview にイベントを送信する .NET Core コンソール アプリケーションの作成方法を紹介します。 

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

次に、Visual Studio で、C# .NET コンソール アプリケーションを作成します。

1. **Visual Studio** を起動します。
2. スタート ウィンドウで、 **[新しいプロジェクトの作成]**  >  **[コンソール アプリ (.NET Framework)]** の順に選択します。 .NET バージョン 4.5.2 以降が必要です。
3. **[プロジェクト名]** に「**PurviewKafkaProducer**」と入力します。
4. **[作成]** を選択してプロジェクトを作成します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. **[新しいプロジェクトの作成]** ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで **[ファイル]** 、 **[新規]** 、 **[プロジェクト]** の順に選択します。 
    1. プログラミング言語として **[C#]** を選択します。
    1. アプリケーションの種類として **[コンソール]** を選択します。 
    1. 結果の一覧から **[コンソール アプリ (.NET Core)]** を選択します。 
    1. 次に、 **[次へ]** を選択します。 


### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs** NuGet パッケージと **Azure.Messaging.EventHubs.Producer** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    

### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Purview アカウントに関連付けられているイベント ハブ名前空間は、Purview アカウントの [プロパティ] タブで、Atlas kafka エンドポイントのプライマリ接続文字列またはセカンダリ接続文字列を見て確認できます。

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="イベント ハブの名前空間":::

    Purview にメッセージを送信するためのイベント ハブ名は、**ATLAS_HOOK** とする必要があります。

3. `Main` メソッドを次の `async Main` メソッドに置き換え、Purview にメッセージをプッシュするための `async ProduceMessage` を追加します。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. プロジェクトをビルドし、エラーがないことを確認します。
6. プログラムを実行し、確認メッセージが表示されるまで待ちます。 

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)を参照してください

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>2 つの列から成る SQL テーブルを作成するサンプル Create Entity JSON メッセージ

```json
    
    {
    "msgCreatedBy":"nayenama",
    "message":{
        "type":"ENTITY_CREATE_V2",
        "user":"admin",
        "entities":{
            "entities":[
                {
                    "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "temporary":false,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table added via Kafka"
                    },
                    "relationshipAttributes":{
                        "columns":[
                            {
                                "guid":"-1102395743156037",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid":"-1102395743156038",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                    },
                    "guid":"-1102395743156036",
                    "version":0
                }
            ],
            "referredEntities":{
                "-1102395743156037":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                        "precision":23,
                        "length":8,
                        "description":"Sales Order ID",
                        "scale":3,
                        "name":"OrderID",
                        "data_type":"int"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156037",
                    "version":2
                },
                "-1102395743156038":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                        "description":"Sales Order Date",
                        "scale":3,
                        "name":"OrderDate",
                        "data_type":"datetime"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156038",
                    "status":"ACTIVE",
                    "createdBy":"ServiceAdmin",
                    "version":0
                }
            }
        }
    },
    "version":{
        "version":"1.0.0"
    },
    "msgCompressionKind":"NONE",
    "msgSplitIdx":1,
    "msgSplitCount":1
}


``` 

## <a name="consume-messages-from-purview"></a>Purview からメッセージを取り込む
このセクションでは、イベント プロセッサを使用してイベント ハブからメッセージを受信する .NET Core コンソール アプリケーションを作成する方法について説明します。 Purview からメッセージを受信するには、ATLAS_ENTITIES イベント ハブを使用する必要があります。イベント プロセッサは、永続的なチェックポイントとイベント ハブからの並列受信を管理するによって、イベント ハブからのイベントの受信を簡素化します。 

> [!WARNING]
> このコードを Azure Stack Hub で実行すると、特定の Storage API バージョンを対象としている場合を除き、実行時エラーが発生します。 これは、Event Hubs SDK では、Azure で利用できる最新の Azure Storage API が使用されますが、Azure Stack Hub プラットフォームではこれを利用できない可能性があるためです。 Azure Stack Hub でサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なる場合があります。 チェックポイント ストアとして Azure Blob Storage を使用している場合は、[Azure Stack Hub ビルドでサポートされている Azure Storage API バージョン](/azure-stack/user/azure-stack-acs-differences?#api-version)を確認し、コード内でそのバージョンを対象にします。 
>
> たとえば、Azure Stack Hub バージョン 2005 上で実行している場合、Storage サービスで利用できる最も高いバージョンは 2019-02-02 となります。 既定では、Event Hubs SDK クライアント ライブラリでは、Azure で利用できる最も高いバージョン (SDK のリリース時点では 2019-07-07) が使用されます。 この場合は、このセクションの手順に従うことに加え、Storage サービス API バージョン 2019-02-02 を対象とするコードを追加する必要もあります。 特定の Storage API バージョンを対象にする方法の例については、[GitHub のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)を参照してください。 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage と BLOB コンテナーを作成する
このクイックスタートでは、チェックポイント ストアとして Azure Storage を使用します。 Azure ストレージ アカウントを作成するには、次の手順に従います。 

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

    接続文字列とコンテナー名を書き留めておきます。 これらは、受信コードで使用します。 


### <a name="create-a-project-for-the-receiver"></a>受信側のプロジェクトを作成する

1. ソリューション エクスプローラー ウィンドウで、 **[EventHubQuickStart]** ソリューションを選択したままにし (または右クリックし)、 **[追加]** をポイントして、 **[新しいプロジェクト]** を選択します。 
1. **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。 
1. **[プロジェクト名]** に「**PurviewKafkaConsumer**」と入力し、 **[作成]** を選択します。 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs.Processor** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Main メソッドを更新する 

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。 山かっこ内のプレースホルダーを、イベント ハブの作成時に取得した適切な値で置き換えます。 山かっこ内のプレースホルダーを、イベント ハブとストレージ アカウントの作成時に取得した適切な値 (アクセス キーとプライマリ接続文字列) で置き換えます。 `{Event Hubs namespace connection string}` がイベント ハブの文字列ではなく名前空間レベルの接続文字列であることを確認します。

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
    
    Purview アカウントに関連付けられているイベント ハブ名前空間は、Purview アカウントの [プロパティ] タブで、Atlas kafka エンドポイントのプライマリ接続文字列またはセカンダリ接続文字列を見て確認できます。

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="イベント ハブの名前空間":::

    Purview にメッセージを送信するためのイベント ハブ名は、**ATLAS_ENTITIES** とする必要があります。

3. `Main` メソッドを次の `async Main` メソッドで置き換えます。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. ここで、次のイベントおよびエラー ハンドラー メソッドをクラスに追加します。 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. プロジェクトをビルドし、エラーがないことを確認します。

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)を参照してください。
6. 受信側アプリを実行します。 

### <a name="sample-message-received-from-purview"></a>Purview から受信するメッセージのサンプル

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> 現在 Atlas は、操作の種類として **ENTITY_CREATE_V2**、**ENTITY_PARTIAL_UPDATE_V2**、**ENTITY_FULL_UPDATE_V2**、**ENTITY_DELETE_V2** をサポートしています。 現在、Purview へのメッセージのプッシュは、既定で有効になっています。 Purview からの読み取りを伴うシナリオの場合は、許可リストへの登録が必要となるため、Microsoft にお問い合わせください。 (Purview アカウントの名前とサブスクリプション ID をご用意ください)。


## <a name="next-steps"></a>次のステップ
GitHub で次のサンプルを確認します。 

- [GitHub にある Event Hubs のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub にあるイベント プロセッサのサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas の通知の概要](https://atlas.apache.org/2.0.0/Notifications.html)
