---
title: .NET を使用して Azure Table Storage と Azure Cosmos DB Table API を使用する
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 08/17/2018
ms.openlocfilehash: 7798af5d667bcf70ba562bb7198f9af570f3005a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044418"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>.NET を使用して Azure Table Storage と Azure Cosmos DB Table API を使用する
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage または Azure Cosmos DB Table API を使用して、NoSQL の構造化データをクラウドに格納できます。スキーマレスのデザインでキー/属性ストアを実現します。 Table Storage と Azure Cosmos DB Table API はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage のデータおよび Azure Cosmos DB Table API へのアクセスは、多くの種類のアプリケーションにとって高速でコスト効率に優れ、また一般に、従来の SQL と比較して、同様の容量のデータを低コストで保存することができます。

Table Storage または Azure Cosmos DB Table API を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータなど、柔軟なデータセットを格納できます。 ストレージ アカウントまたは Table API アカウントの容量の上限を超えない限り、テーブルには任意の数のエンティティを保存でき、ストレージ アカウントまたは Table API アカウントには任意の数のテーブルを含めることができます。

### <a name="about-this-sample"></a>このサンプルについて
このサンプルでは、Azure Table Storage および Table API の一般的な用途における [.NET 用 Microsoft Azure CosmosDB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)の使い方について説明します。 パッケージの名前は、Azure Cosmos DB で使用することを示していますが、パッケージは Azure Cosmos DB Table API と Azure Tables ストレージの両方に使用できます。また、各サービスは一意のエンドポイントを持っています。 次の方法を示す C# の例を使用して、これらのシナリオについて説明します。
* テーブルの作成と削除
* 行の挿入、更新、削除
* テーブルの照会

## <a name="prerequisites"></a>前提条件

このサンプルの作業を行うためには、次のものが必要になります。

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 用 Azure Storage Common ライブラリ (プレビュー)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)。 - 運用環境でサポートされている必須のプレビュー パッケージ。 
* [.NET 用 Microsoft Azure CosmosDB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) - このライブラリは、現在、.NET Standard だけで利用できます。まだ .NET Core では利用できません。
* [.NET 用 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure Storage アカウント](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>その他のサンプル
Table Storage を使用したその他の例については、「 [Getting Started with Azure Table Storage in .NET (.NET での Azure Blob Storage の使用)](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)」を参照してください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
* 最初の Azure ストレージ アカウントを作成する最も簡単な方法は、[Azure Portal](https://portal.azure.com) を利用することです。 詳細については、「 [ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。

* Azure Storage アカウントは、[Azure PowerShell](../storage/common/storage-powershell-guide-full.md)、[Azure CLI](../storage/common/storage-azure-cli.md)、または [.NET 用ストレージ リソース プロバイダー クライアント ライブラリ](/dotnet/api/microsoft.azure.management.storage)を使用して作成することもできます。

* 現時点でストレージ アカウントを作成しない場合は、Azure ストレージ エミュレーターを使用して、ローカル環境でコードの実行とテストを行うこともできます。 詳細については、「 [開発とテストのための Azure のストレージ エミュレーター使用](../storage/common/storage-use-emulator.md)」を参照してください。

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

### <a name="create-a-windows-console-application-project"></a>Windows コンソール アプリケーション プロジェクトの作成
Visual Studio で、新しい Windows コンソール アプリケーションを作成します。 次の手順では、Visual Studio 2017 でコンソール アプリケーションを作成する方法を説明します。 この手順は Visual Studio の他のバージョンでも同様です。

1. **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。
2. **[インストール済み]** > **[Visual C#]** > **[Windows クラシック デスクトップ]** の順に選択します。
3. **[コンソール アプリ (.NET Framework)]** を選択します。
4. **[名前]** フィールドに、アプリケーションの名前を入力します。
5. **[OK]** を選択します。

このサンプルのすべてのコード例は、コンソール アプリケーションの `Program.cs` ファイルの `Main()` メソッドに追加できます。

Azure クラウド サービス、Azure Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなど、どの種類の .NET アプリケーションでも Azure CosmosDB Table ライブラリを使用できます。 このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

### <a name="install-the-required-nuget-packages"></a>必須 NuGet パッケージをインストールする
このサンプルを完了するには、プロジェクトで参照する必要がある、推奨のパッケージが 3 つあります。

* [.NET 用 Azure Storage Common ライブラリ (プレビュー)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common)。 - 9.0.0.1 以下 (<= 9.0.0.1) のバージョンを使用してください。

* [.NET 用 Microsoft Azure Cosmos DB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。 このパッケージを使用すると、Azure Table ストレージ アカウントまたは Azure Cosmos DB Table API アカウント内のデータ リソースにプログラムでアクセスできます。 このライブラリは、現在、.NET Standard だけで利用できます。まだ .NET Core では利用できません。

* [.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/):このパッケージには、アプリケーションの実行場所に関係なく、構成ファイルの接続文字列を解析するためのクラスが用意されています。

NuGet パッケージを取得するには、次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. 「Microsoft.Azure.Storage.Common」をオンラインで検索し、<= 9.0.0.1 のバージョンを選択し、**[インストール]** を選択して .NET 用 Azure Storage Common ライブラリ (プレビュー) とその依存関係をインストールします。 これはプレビュー パッケージなので、**[プレリリースを含める]** チェックボックスをオンにします。
3. 「Microsoft.Azure.CosmosDB.Table」をオンラインで検索し、**[インストール]** を選択して Microsoft Azure CosmosDB Table ライブラリをインストールします。
4. "WindowsAzure.ConfigurationManager" をオンラインで検索し、**[インストール]** を選択して Microsoft Azure Configuration Manager ライブラリをインストールします。

> [!NOTE]
> .NET 用 Storage Common ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet で入手できる ODataLib パッケージで解決されます。 ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。 ストレージ クライアント ライブラリで使用される ODataLib パッケージは、[OData](https://nuget.org/packages/Microsoft.Data.OData/)、[Edm](https://nuget.org/packages/Microsoft.Data.Edm/)、[Spatial](https://nuget.org/packages/System.Spatial/) です。 これらのライブラリが Azure Table ストレージ クラスで使用されるときは、Storage Common ライブラリを使用したプログラミングの必須の依存関係です。
> 
> 

> [!TIP]
> Azure テーブル ストレージに既に慣れている開発者は、[WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) パッケージを過去に使用している可能性があります。 WindowsAzure.Storage パッケージはまだサポートされていますが、新しいテーブル アプリケーションでは必ず、[Azure Storage Common ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common)と [Azure Cosmos DB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)を使用することをお勧めします。 WindowsAzure.Storage ライブラリを使用する場合は、使用するステートメントに Microsoft.WindowsAzure.Storage.Table を含めてください。
>
>

### <a name="determine-your-target-environment"></a>ターゲット環境の決定
このガイドの例を実行するための環境オプションとして次の 3 つがあります。

* クラウド内の Azure ストレージ アカウントに対してコードを実行できます。 
* クラウド内の Azure Cosmos DB アカウントに対してコードを実行できます。
* Azure ストレージ エミュレーターに対してコードを実行できます。 ストレージ エミュレーターは、クラウド内の Azure ストレージ アカウントをエミュレートするローカル環境です。 エミュレーターを使用すると、アプリケーションの開発中にコードのテストとデバッグを無料で実行できます。 エミュレーターでは既知のアカウントとキーを使用します。 詳細については、「[Azure ストレージ エミュレーターを使用した開発とテスト](../storage/common/storage-use-emulator.md)」を参照してください。

クラウドのストレージ アカウントをターゲットにする場合は、Azure Portal からストレージ アカウントのプライマリ アクセス キーをコピーします。 詳細については、「[ストレージ アカウントのアクセス キー](../storage/common/storage-account-manage.md#access-keys)」を参照してください。

> [!NOTE]
> ストレージ エミュレーターをターゲットにすると、Azure Storage に関連する利用料金の発生を回避できます。 ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このサンプルを実行するための利用料金はごくわずかです。
> 
> 

Azure Cosmos DB アカウントをターゲットにする場合は、Azure Portal から Table API アカウントのプライマリ アクセス キーをコピーします。 詳細については、「[接続文字列を更新する](create-table-dotnet.md#update-your-connection-string)」を参照してください。

### <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
.NET 用 Azure Storage Common ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。 ストレージ接続文字列を管理するには、構成ファイルの中に保持することをお勧めします。 

接続文字列の詳細については、[Azure Storage の接続文字列の構成](../storage/common/storage-configure-connection-string.md)に関するページを参照してください。

> [!NOTE]
> アカウント キーは、ストレージ アカウントの root パスワードに似ています。 ストレージ アカウント キーは常に慎重に保護してください。 このキーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキスト ファイルに保存したりしないでください。 キーが侵害されたと思われる場合は、Azure Portal を使用してキーを再生成してください。
> 

接続文字列を構成するには、Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。 次に示す `<appSettings>` 要素の内容を追加します。 `account-name` をアカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

たとえば、Azure Storage アカウントを使用している場合は、次のように構成設定が表示されます。

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

Azure Cosmos DB アカウントを使用している場合は、次のように構成設定が表示されます。

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

ストレージ エミュレーターをターゲットとする場合、既知のアカウントの名前とキーにマップされるショートカットを使用できます。 この場合、接続文字列の設定は次のようになります。

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>using ディレクティブを追加する
次の **using** ディレクティブを `Program.cs` ファイルの先頭に追加します。

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>接続文字列を解析する
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Table service クライアントを作成する
[CloudTableClient][dotnet_CloudTableClient] クラスを使用すると、Table Storage に格納されているテーブルとエンティティを取得できます。 Table service クライアントを作成する方法の 1 つを次に示します。

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

これで、Table Storage に対してデータの読み取りと書き込みを実行するコードを記述する準備が整いました。

## <a name="create-a-table"></a>テーブルを作成する
この例は、テーブルがない場合に、キューを作成する方法を示しています。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティは、[TableEntity][dotnet_TableEntity] から派生するカスタム クラスを使用して C# オブジェクトにマップされます。 エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速にクエリを実行できます。一方、多様なパーティション キーを使用すると、並列操作のスケーラビリティが向上します。 テーブルに格納するエンティティは、[TableEntity][dotnet_TableEntity] クラスから派生した型など、サポートされている型である必要があります。 テーブルに格納するエンティティのプロパティは、その型のパブリック プロパティであること、また、値の取得と設定の両方に対応していることが必要です。 また、エンティティ型で、パラメーターのないコンストラクターを "*必ず*" 公開する必要があります。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

エンティティに関連するテーブル操作は、テーブルの作成に関するセクションで作成した [CloudTable][dotnet_CloudTable] オブジェクトを使用して実行されます。 実行する操作は、[TableOperation][dotnet_TableOperation] オブジェクトによって表されます。 次のコード例では、まず [CloudTable][dotnet_CloudTable] オブジェクトを作成し、次に **CustomerEntity** オブジェクトを作成します。 その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する [TableOperation][dotnet_TableOperation] オブジェクトを作成します。 最後に、[CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute] を呼び出して操作を実行します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作でエンティティのバッチをテーブルに挿入できます。 バッチ操作に関しては、次の事項にも留意してください。

* 更新、削除、および挿入を同じ 1 回のバッチ操作で実行できます。
* 1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。
* クエリをバッチ操作として実行することもできますが、バッチ内の唯一の操作である必要があります。

次のコード例は、2 つのエンティティ オブジェクトを作成し、[Insert][dotnet_TableBatchOperation_Insert] メソッドを使用して [TableBatchOperation][dotnet_TableBatchOperation] に追加します。 その後、[CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] を呼び出して操作を実行します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、[TableQuery][dotnet_TableQuery] オブジェクトを使用します。 次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する
パーティション内の一部のエンティティのみを照会する場合は、パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。 次のコード例は、2 つのフィルターを使用して、行キー (名) がアルファベットの "E" より前の文字で始まる、"Smith" というパーティション内のすべてのエンティティを取得し、クエリ結果を出力します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、[TableOperation][dotnet_TableOperation] を使用して、"Ben Smith" というユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、[TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] の戻り値は **CustomerEntity** オブジェクトです。 クエリでパーティション キーと行キーの両方を指定することが、Table service から単一のエンティティを取得するための最速の方法です。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>エンティティを置換する
エンティティを更新するには、そのエンティティを Table service から取得し、エンティティ オブジェクトを変更して、変更を Table service に戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 このコードでは、[Insert][dotnet_TableOperation_Insert] を呼び出す代わりに [Replace][dotnet_TableOperation_Replace] を使用しています。 [Replace][dotnet_TableOperation_Replace] により、サーバーでエンティティが完全に置換されます。ただし、エンティティを取得した後にサーバーでエンティティが変更された場合は、操作が失敗します。 このエラーは、取得と更新の間にアプリケーションの別のコンポーネントによって行われた変更が意図せず上書きされるのを防ぐためのものです。 このエラーを正しく処理するには、もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、再び [Replace][dotnet_TableOperation_Replace] 操作を実行します。 次のセクションでは、この動作をオーバーライドする方法を説明します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>エンティティを挿入または置換する
[Replace][dotnet_TableOperation_Replace] 操作は、サーバーからエンティティを取得した後にエンティティが変更されていると失敗します。 さらに、[Replace][dotnet_TableOperation_Replace] 操作を成功させるためには、先にエンティティをサーバーから取得する必要があります。 しかし、サーバーにエンティティが存在するかどうかわからないが、現在格納されている値は不適切である場合があります。 このため、更新ですべての値を上書きする必要があります。 そのような場合は、[InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 操作を使用します。 この操作は、最終更新日時に関係なく、エンティティが存在しない場合は挿入し、存在する場合は置換します。

以下のコード例では、"Fred Jones" のユーザー エンティティを作成し、"people" テーブルに挿入しています。 次に、同じパーティション キー (Jones) と行キー (Fred) で、PhoneNumber プロパティの値のみ異なるエンティティを [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 操作でサーバーに保存します。 [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] を使っているため、そのプロパティの値がすべて置き換えられます。 ただし、ここで "Fred Jones" エンティティが仮にまだテーブルに存在していなければ、置換ではなく挿入となります。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブル クエリでは、エンティティのすべてのプロパティではなくごくわずかのプロパティだけをエンティティから取得できます。 プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 次のコードのクエリは、テーブル内のエンティティの電子メール アドレスだけを返します。 これは、[DynamicTableEntity][dotnet_DynamicTableEntity] のクエリと [EntityResolver][dotnet_EntityResolver] を使用して行われます。 プロジェクションはストレージ エミュレーターではサポートされていません。したがって、このコードは Table service のアカウントを使用している場合にのみ機能します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。 次のコードは、ユーザー エンティティを取得して削除します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 削除されたテーブルは、削除後の一定期間は再作成できなくなります。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>ページ内のエンティティを非同期的に取得する
多数のエンティティを読み取る場合、すべてが返されるまで待機するのではなく取得後に処理または表示するには、セグメント化されたクエリを使用してエンティティを取得できます。 この例は、大きな結果のセットが返されるのを待機している間に実行がブロックされないように、Async-Await パターンを使用して結果をページに返す方法を示しています。 .NET での Async-Await パターンの使用方法の詳細については、 [Async および Await を使用した非同期プログラミング (C# および Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)に関するページをご覧ください。

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>次の手順
これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* Table Storage のサンプルについては、「 [Getting Started with Azure Table Storage in .NET (.NET での Azure Blob Storage の使用)](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* 利用可能な API の詳細については、Table service のリファレンス ドキュメントを参照してください。
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API リファレンス](https://msdn.microsoft.com/library/azure/dd179355)
* Azure Storage で作業するために記述したコードを簡略化する方法については、 [Azure WebJobs SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
* [.NET を使用して Azure Blob Storage を使用する](../storage/blobs/storage-dotnet-how-to-use-blobs.md) 」を参照してください。
* リレーショナル データを格納する方法については、「[.NET (C#) を使用して SQL Database に接続する](../sql-database/sql-database-develop-dotnet-simple.md)」を参照してください。

[Download and install the Azure SDK for .NET]: /develop/net/

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
