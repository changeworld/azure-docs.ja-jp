---
title: C# による Azure Data Explorer へのエンドツーエンドの BLOB の取り込み
description: この記事では、C# を使用するエンドツーエンドの例を用いて、Azure Data Explorer に BLOB を取り込む方法について説明します。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: e22621083a44555cb3eda615c610f673cd841ec1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581850"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>C# による Azure Data Explorer へのエンドツーエンドの BLOB の取り込み

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer は、ログと利用統計情報のための高速でスケーラブルなデータ探索サービスです。 この記事では、Azure Blob Storage から Azure Data Explorer にデータを取り込む方法について、エンドツーエンドの例を示します。 

プログラムによって、リソース グループ、ストレージ アカウントとコンテナー、イベント ハブ、および Azure Data Explorer クラスターとデータベースを作成する方法を説明します。 また、プログラムによって、新しいストレージ アカウントからデータを取り込むよう Azure Data Explorer を構成する方法についても説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="install-c-nuget"></a>C# NuGet をインストールする

* [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/) をインストールします。
* [Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager) をインストールします。
* [Microsoft.Azure.Management.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/) をインストールします。
* [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) をインストールします。
* 認証用の [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) をインストールします。

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>コード例 

次のコード例は、Azure Data Explorer へのデータ インジェストが実行されることになる段階ごとのプロセスを示しています。 

最初にリソース グループを作成します。 ストレージ アカウントとコンテナー、イベント ハブ、Azure Data Explorer クラスターとデータベースなどの Azure リソースも作成します。 次に、Azure Data Explorer データベースで、テーブルと列のマッピングと一緒に Azure Event Grid サブスクリプションを作成します。 最後に、データ接続を作成し、新しいストレージ アカウントからデータを取り込むよう Azure Data Explorer を構成します。 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.");
var parameters = $"{{\"eventHubNamespaceName\":{{\"value\":\"{eventHubNamespaceName}\"}},\"eventHubName\":{{\"value\":\"{eventHubName}\"}},\"storageAccountName\":{{\"value\":\"{storageAccountName}\"}},\"containerName\":{{\"value\":\"{storageContainerName}\"}},\"kustoClusterName\":{{\"value\":\"{kustoClusterName}\"}},\"kustoDatabaseName\":{{\"value\":\"{kustoDatabaseName}\"}}}}";
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **設定** | **フィールドの説明** |
|---|---|---|
| tenantId | テナント ID。 これはディレクトリ ID とも呼ばれます。|
| subscriptionId | リソースの作成に使用するサブスクリプション ID。|
| clientId | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| clientSecret | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |

## <a name="test-the-code-example"></a>コード例をテストする

1. ストレージ アカウントにファイルをアップロードします。

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**設定** | **フィールドの説明**|
    |---|---|---|
    | storageConnectionString | プログラムによって作成されたストレージ アカウントの接続文字列。|

2. Azure Data Explorer でテスト クエリを実行します。

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループを削除し、リソースをクリーンアップするには、次のコマンドを実行します。

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>次の手順

*  クラスターとデータベースを作成するその他の方法については、[Azure Data Explorer クラスターとデータベースの作成](create-cluster-database-csharp.md)に関するページを参照してください。
* 取り込み方法の詳細については、[Azure Data Explorer でのデータ インジェスト](ingest-data-overview.md)に関するページを参照してください。
* Web アプリケーションの詳細については、「[クイックスタート:Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)」を参照してください。
* Kusto クエリ言語で[クエリを記述します](write-queries.md)。