---
title: クイック スタート:.NET での Table API の使用 - Azure Cosmos DB
description: このクイック スタートでは、Azure.Data.Tables SDK を使用して .NET アプリケーションから Azure Cosmos DB Table API にアクセスする方法について説明します。
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/26/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: e76341f8d3d0e7b7c67be6a19ab3fb8c5b902794
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324177"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>クイック スタート:.NET SDK と Azure Cosmos DB で Table API アプリをビルドする

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

このクイック スタートでは、.NET アプリケーションから Azure Cosmos DB [Table API](introduction.md) にアクセスする方法について説明します。  Cosmos DB Table API はスキーマレス データ ストアであり、これによりアプリケーションは構造化された NoSQL データをクラウドに格納できます。  データはスキーマレス デザインで格納されるので、新しい属性を持つオブジェクトがテーブルに追加されると、新しいプロパティ (列) がテーブルに自動的に追加されます。

.NET アプリケーションでは [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) NuGet パッケージを使用して Cosmos DB Table API にアクセスできます。  [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) パッケージは、.NET Framework (4.7.2 以降) および .NET Core (2.0 以降) アプリケーションの両方で動作する [.NET Standard 2.0](/dotnet/standard/net-standard) ライブラリです。

## <a name="prerequisites"></a>前提条件

サンプル アプリケーションは[.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1) で記述されていますが、原則は .NET Framework および .NET Core アプリケーションの両方に適用されます。  [Visual Studio](https://www.visualstudio.com/downloads/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)、[Visual Studio Code](https://code.visualstudio.com/) のいずれかを IDE として使用できます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>サンプル アプリケーション

このチュートリアルのサンプル アプリケーションは、リポジトリ ([https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet)) からクローンまたはダウンロードできます。  サンプル リポジトリには、スターター アプリケーションと完成したアプリケーションの両方が含まれています。

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

サンプル アプリケーションでは、気象データを例に Table API の機能を説明しています。 気象観測を表すオブジェクトの格納と取得には Table API が使用されます。これには、Table AIP のスキーマレス機能を示すために追加のプロパティを持つオブジェクトを格納する操作が含まれます。

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="Table API を使用して Cosmos DB テーブルへ格納されたデータを示す、完成したアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1 - Azure Cosmos DB アカウントを作成する

まず、自分のアプリケーションで使用するテーブルが組み込まれる Cosmos DB Table API アカウントを作成します。  これを行うには、Azure portal、Azure CLI、または Azure PowerShell を使用します。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure portal](https://portal.azure.com/) にログインし、これらの手順に従って Cosmos DB アカウントを作成します。

| 手順    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="上部のツール バーにある検索ボックスを使用して、Azure で Cosmos DB アカウントを検索する方法を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="Azure の Cosmos DB アカウントのページでの [作成] ボタンの場所を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="[Azure テーブル] オプションが選択すべき正しいオプションとして示されているスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-4.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="Cosmos DB アカウント作成ページのフィールドへの入力方法を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB アカウントを作成するには [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) コマンドを使用します。 Cosmos DB 内でテーブル ストレージを有効にするため、`--capabilities EnableTable` オプションを含める必要があります。  すべての Azure リソースが 1 つのリソース グループに含まれているようにする必要があるため、次のコード スニペットでは、Cosmos DB アカウントのリソース グループも作成されます。

Cosmos DB アカウント名の長さは 3 ～ 44 文字で、小文字、数字、ハイフン (-) 文字のみを使用できます。  Cosmos DB アカウント名は、Azure 全体で一意である必要があります。

Azure CLI コマンドは、[Azure Cloud Shell](https://shell.azure.com)、または [Azure CLI がインストールされている](/cli/azure/install-azure-cli)ワークステーションで実行できます。

通常、Cosmos DB アカウントの作成プロセスには数分かかります。

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Cosmos DB アカウントを作成するには、[New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) コマンドレットを使用します。 Cosmos DB 内でテーブル ストレージを有効にするため、`-ApiKind "Table"` オプションを含める必要があります。  すべての Azure リソースが 1 つのリソース グループに含まれているようにする必要があるため、次のコード スニペットでは、Azure Cosmos DB アカウントのリソース グループも作成されます。

Azure Cosmos DB アカウント名の長さは 3 ～ 44 文字で、小文字、数字、ハイフン (-) 文字のみを使用できます。  Azure Cosmos DB アカウント名は、Azure 全体で一意である必要があります。

Azure PowerShell コマンドは、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell がインストールされている](/powershell/azure/install-az-ps)ワークステーションで実行できます。

通常、Cosmos DB アカウントの作成プロセスには数分かかります。

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2 - テーブルを作成する

次に、アプリケーションで使用するテーブルを Cosmos DB アカウント内に作成する必要があります。  従来のデータベースとは異なり、指定する必要があるのはテーブルの名前のみであり、テーブルのプロパティ (列) は指定する必要はありません。  データがテーブルに読み込まれると、必要に応じてプロパティ (列) が自動的に作成されます。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure portal](https://portal.azure.com/) で次の手順を実行して、Cosmos DB アカウント内にテーブルを作成します。

| 手順    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](./includes/create-table-dotnet/create-cosmos-table-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="上部のツール バーにある検索ボックスを使用して、Cosmos DB アカウントを検索する方法を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](./includes/create-table-dotnet/create-cosmos-table-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="[テーブルの追加] ボタンの位置を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](./includes/create-table-dotnet/create-cosmos-table-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="[新しいテーブル] ダイアログ ボックスで Cosmos DB テーブルの情報の入力方法を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB にテーブルを作成するには [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) コマンドを使用します。

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Cosmos DB にテーブルを作成するには [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) コマンドレットを使用します。

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3 - Cosmos DB の接続文字列を取得する

Cosmos DB 内のテーブルにアクセスするには、アプリケーションに CosmosDB Storage アカウントのテーブル接続文字列が必要です。  この接続文字列は、Azure portal、Azure CLI、または Azure PowerShell を使用して取得できます。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

| 手順    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](./includes/create-table-dotnet/get-cosmos-connection-string-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="Cosmos DB ページの接続文字列リンクの位置を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](./includes/create-table-dotnet/get-cosmos-connection-string-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="選択してアプリケーションで使用する接続文字列を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して主テーブル ストレージ接続文字列を取得するには、オプション `--type connection-strings` を指定した [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) コマンドを使用します。  このコマンドは、[JMESPath クエリ](https://jmespath.org/)を使用して主テーブル接続文字列のみを表示します。

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell を使用して主テーブル ストレージ接続文字列を取得するには、[Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) コマンドレットを使用します。

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

Cosmos DB アカウントの接続文字列はアプリケーションのシークレットと見なされ、他のアプリケーションのシークレットやパスワードと同様に保護する必要があります。  この例では[シークレット マネージャー ツール](/aspnet/core/security/app-secrets#secret-manager)を使用して、開発中に接続文字列を保管し、アプリケーションで使用できるようにします。  シークレット マネージャー ツールは Visual Studio と .NET CLI のいずれからもアクセスできます。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio からシークレット マネージャー ツールを開くには、プロジェクトを右クリックし、コンテキスト メニューから **[ユーザー シークレットの管理]** を選択します。  これにより、プロジェクトの *secrets.json* ファイルが開きます。  このファイルの内容を、以下に示す JSON に置き換え、Cosmos DB テーブルの接続文字列を入力します。

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

シークレット マネージャーを使用するには、まず `dotnet user-secrets init` コマンドを使用してプロジェクト用に初期化する必要があります。

```dotnetcli
dotnet user-secrets init
```

次に、`dotnet user-secrets set` コマンドを使用して Cosmos DB テーブル接続文字列をシークレットとして追加します。

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4 - Azure.Data.Tables NuGet パッケージをインストールする

.NET アプリケーションから Cosmos DB Table API にアクセスするには、[Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables) NuGet パッケージをインストールします。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5 - Startup.cs で Table クライアントを構成する

Azure SDK は、クライアント オブジェクトを使用して Azure と通信し、Azure に対してさまざまな操作を実行します。  [TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトは、Cosmos DB Table API と通信するために使用されるオブジェクトです。

通常、アプリケーションではテーブルごとに 1 つの [TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトが作成され、アプリケーション全体で使用されます。  このためには、依存関係の挿入 (DI) を使用し、[TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトをシングルトンとして登録することをお勧めします。  Azure SDK で DI を使用する方法の詳細については、「[Azure SDK for .NET での依存関係の挿入](/dotnet/azure/sdk/dependency-injection)」を参照してください。

アプリケーションの `Startup.cs` ファイルで、次のコード スニペットと一致するように ConfigureServices() メソッドを編集します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

また、次の using ステートメントを Startup.cs ファイルの先頭に追加する必要があります。

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6 - Cosmos DB テーブル操作を実装する

サンプル アプリケーションの Cosmos DB テーブル操作はすべて、*Services* ディレクトリにあるクラス `TableService` に実装されます。  `Azure.Data.Tables` SDK パッケージ内のオブジェクトを操作するには、このファイルの先頭にある `Azure` および `Azure.Data.Tables` 名前空間をインポートする必要があります。

```csharp
using Azure;
using Azure.Data.Tables;
```

`TableService` クラスの先頭に、[TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトのメンバー変数と、[TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトをクラスに挿入するためのコンストラクターを追加します。

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>テーブルから行を取得する

[TableClient](/dotnet/api/azure.data.tables.tableclient) クラスに含まれている [Query](/dotnet/api/azure.data.tables.tableclient.query) メソッドにより、テーブルから行を選択できるようになります。  この例では、このメソッドにパラメーターが渡されないため、テーブルからすべての行が選択されます。

このメソッドは、返されるモデル クラス データを指定する [ITableEntity](/dotnet/api/azure.data.tables.itableentity) 型のジェネリック パラメーターも取ります。 このケースでは、組み込みの [TableEntity](/dotnet/api/azure.data.tables.itableentity) クラスが使用されます。つまり、`Query` メソッドは結果として `Pageable<TableEntity>` コレクションを返します。

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

`Azure.Data.Tables` パッケージで定義されている [TableEntity](/dotnet/api/azure.data.tables.itableentity) クラスには、テーブルのパーティション キーと行キーの値のプロパティが含まれています。  この 2 つの値により、テーブル内の行の一意のキーが形成されます。  このサンプル アプリケーションでは、観測所 (市町村) の名前がパーティション キーに格納され、観測日時が行キーに格納されます。  その他のプロパティ (温度、湿度、風速) はすべて、`TableEntity` オブジェクトのディクショナリに格納されます。

一般的に、[TableEntity](/dotnet/api/azure.data.tables.tableentity) オブジェクトは各自で定義するオブジェクトにマップします。  サンプル アプリケーションでは、この目的で *Models* ディレクトリに `WeatherDataModel` クラスが定義されます。  このクラスには観測所名と観測日のプロパティが含まれており、これらのプロパティにパーティション キーと行キーがマップされます。これにより、これらの値についてよりわかりやすいプロパティ名が提供されます。  そして、オブジェクトのその他のプロパティをすべて格納するため、ディクショナリが使用されます。  これはテーブル ストレージを使用する場合の一般的なパターンです。行には任意の数のプロパティを追加でき、モデル オブジェクトでそれらをすべて取り込む必要があるためです。  このクラスには、クラスのプロパティをリストするメソッドも含まれています。

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

`MapTableEntityToWeatherDataModel` メソッドは、[TableEntity](/dotnet/api/azure.data.tables.itableentity) オブジェクトを `WeatherDataModel` オブジェクトにマップするために使用されます。  [TableEntity](/dotnet/api/azure.data.tables.itableentity) オブジェクトには、オブジェクトのテーブルに含まれているすべてのプロパティ名 (実質的にはテーブル内のこの行の列名) を取得するための[Keys](/dotnet/api/azure.data.tables.tableentity.keys)プロパティが含まれています。  `MapTableEntityToWeatherDataModel` メソッドは、`PartitionKey`、`RowKey`、`Timestamp`、および `Etag` の各プロパティを直接マップし、`Keys` プロパティを使用して `TableEntity` オブジェクト内の他のプロパティを反復処理し、これらのプロパティのうち、既に直接マップされているプロパティを除くすべてを `WeatherDataModel` オブジェクトにマップします。

次のコード ブロックと一致するように `MapTableEntityToWeatherDataModel` メソッドのコードを編集します。

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>テーブルから返された行をフィルター処理する

テーブルから返された行をフィルター処理するには、OData スタイルのフィルター文字列を [Query](/dotnet/api/azure.data.tables.tableclient.query) メソッドに渡します。 たとえば、2021 年 7 月 1 日午前 0 時から 2021 年 7 月 2 日午前 0 時 (午前 0 時を含む) までの Chicago の気象記録をすべて取得するには、次のフィルター文字列を渡します。

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

すべての OData フィルター演算子については、OData Web サイトの [Filter System Query Option](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/) に関するセクションで確認できます。

このサンプル アプリケーションでは、`FilterResultsInputModel` オブジェクトは、ユーザーが指定したフィルター条件をすべて取り込むように設計されています。

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

このオブジェクトが `TableService` クラスの `GetFilteredRows` メソッドに渡されると、null 以外のプロパティ値ごとにフィルター文字列が作成されます。  次に、"and" 句を使用してすべての値を結合することで、結合フィルター文字列が作成されます。  この結合フィルター文字列は [TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトの [Query](/dotnet/api/azure.data.tables.tableclient.query) メソッドに渡され、フィルター文字列に一致する行だけが返されます。  同様のメソッドをコード内で使用して、各自のアプリケーションで必要に応じて適切なフィルター文字列を作成できます。

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>TableEntity オブジェクトを使用してデータを挿入する

テーブルにデータを追加する最も簡単な方法は [TableEntity](/dotnet/api/azure.data.tables.itableentity) オブジェクトを使用する方法です。  この例では、データが入力モデル オブジェクトから [TableEntity](/dotnet/api/azure.data.tables.itableentity) オブジェクトにマップされます。  入力オブジェクトで観測所名と観測日時を表すプロパティは、[PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) および [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) プロパティにそれぞれマップされ、これらによりテーブル内の行の一意のキーが形成されます。  その後、入力モデル オブジェクトの追加プロパティが TableEntity オブジェクトのディクショナリ プロパティにマップされます。  最後に [TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトの [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) メソッドを使用して、テーブルにデータが挿入されます。

サンプル アプリケーションの `InsertTableEntity` クラスを変更して、次のコードを含めます。

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>TableEntity オブジェクトを使用してデータをアップサートする

テーブルに既に存在するパーティション キーと行キーの組み合わせでそのテーブルに行を挿入しようとすると、エラーが発生します。  このため多くの場合、テーブルに行を追加するときには AddEntity メソッドの代わりに [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) を使用することが推奨されます。  指定されたパーティション キーと行キーの組み合わせがテーブルに既に存在する場合は、[UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) メソッドにより既存の行が更新されます。  それ以外の場合は行がテーブルに追加されます。

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>可変プロパティを使用してデータを挿入またはアップサートする

Cosmos DB Table API を使用する利点の 1 つは、テーブルに読み込まれるオブジェクトに新しいプロパティが含まれている場合、それらのプロパティがテーブルに自動的に追加され、値が Cosmos DB に格納されることです。  従来のデータベースのように、ALTER TABLE などの DDL ステートメントを実行して列を追加する必要はありません。

このモデルを使用すると、時間の経過とともに取り込む必要があるデータを追加または変更する可能性のあるデータ ソースを扱う場合や、異なる入力によりアプリケーションに異なるデータを提供する場合に、アプリケーションに柔軟性が加わります。 サンプル アプリケーションでは、基本的な気象データの他にいくつかの追加の値も送信する観測所をシミュレートできます。 このような新しいプロパティを持つオブジェクトが初めてテーブルに格納されるときに、対応するプロパティ (列) がテーブルに自動的に追加されます。

サンプル アプリケーションでは、オブジェクトのあらゆるプロパティに対応できるように、内部ディクショナリをに基づいて `ExpandableWeatherObject` クラスが作成されています。  このクラスは、オブジェクトに任意のプロパティ セットが含まれている必要がある場合の一般的なパターンを表します。

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Table API を使用してこのようなオブジェクトを挿入またはアップサートするには、展開可能なオブジェクトのプロパティを[TableEntity](/dotnet/api/azure.data.tables.tableentity) オブジェクトにマップし、必要に応じて[TableClient](/dotnet/api/azure.data.tables.tableclient)オブジェクトの [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) または [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) メソッドを使用します。

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>エンティティを更新する

エンティティを更新するには、[TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトの [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) メソッドを呼び出します。  Table API を使用して格納されるエンティティ (行) には任意のプロパティ セットが含まれている可能性があるため、多くの場合、前述の `ExpandableWeatherObject` と同様に Dictionary オブジェクトに基づいて更新オブジェクトを作成すると便利です。  ここで唯一異なる点は、更新中の同時実行制御に使用される `Etag` プロパティを追加することです。

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

サンプル アプリケーションでは、このオブジェクトは `TableService` クラスの `UpdateEntity` メソッドに渡されます。  このメソッドは、最初に [TableClient](/dotnet/api/azure.data.tables.tableclient) の [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity) メソッドを使用して Table API から既存のエンティティを読み込みます。  その後、そのエンティティ オブジェクトを更新し、`UpdateEntity` メソッドを使用して更新内容をデータベースに保存します。  [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) メソッドがオブジェクトの現在の Etag を取得して、オブジェクトが最初に読み込まれてから変更されていないことを確認する方法に注目してください。  それに関係なくエンティティを更新する必要がある場合は、`Etag.Any` の値を `UpdateEntity` メソッドに渡します。

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>エンティティを削除する

テーブルからエンティティを削除するには、[TableClient](/dotnet/api/azure.data.tables.tableclient) オブジェクトの [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity) メソッドを呼び出します。その際に、このオブジェクトのパーティション キーと行キーを指定します。

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7 - コードを実行する

サンプル アプリケーションを実行して、Cosmos DB Table API を操作します。  アプリケーションを初めて実行する場合にはテーブルが空であるため、データがありません。  アプリケーションの上部にあるいずれかのボタンを使用して、テーブルにデータを追加します。

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="Table API を使用して Cosmos DB にデータを挿入するためのボタンの位置を示すアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

**[Insert using Table Entity]\(テーブル エンティティを使用して挿入\)** ボタンを選択すると、`TableEntity` オブジェクトを使用して新しい行を挿入またはアップサートできるダイアログが開きます。

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="TableEntity オブジェクトを使用してデータを挿入するために使用されるダイアログ ボックスを示すアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

**[Insert using Expandable Data]\(展開可能なデータを使用して挿入\)** ボタンを選択すると、カスタム プロパティを持つオブジェクトを挿入できるダイアログが表示され、Cosmos DB Table API によって必要に応じてプロパティ (列) がテーブルに自動的に追加される方法が示されます。  *[カスタム フィールドの追加]* ボタンを使用して、1 つ以上の新しいプロパティを追加し、この機能を示します。

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="カスタム フィールドのあるオブジェクトを使用してデータを挿入するために使用されるダイアログ ボックスを示すアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

**[Insert Sample Data]\(サンプル データの挿入\)** ボタンを使用して、一部のサンプル データを Cosmos DB テーブルに読み込みます。

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="サンプル データ挿入ボタンの位置を示すアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

上部のメニューの **[Filter Results]\(結果のフィルター処理\)** 項目を選択すると、[Filter Results]\(結果のフィルター処理\) ページが表示されます。  フィルター句を作成して Cosmos DB Table API に渡す方法を示すため、このページでフィルター条件を入力します。

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="結果のフィルター処理ページが表示され、そのページへの移動に使用するメニュー項目が強調表示されているアプリケーションのスクリーンショット。" lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

サンプル アプリケーションの使用が完了したら、この記事に関連するすべての Azure リソースを Azure アカウントから削除する必要があります。  このためには、リソース グループを削除します。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure portal](https://portal.azure.com/) を使用してリソース グループを削除するには、次の手順を実行します。

| 手順    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](./includes/create-table-dotnet/remove-resource-group-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="リソース グループを検索する方法を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](./includes/create-table-dotnet/remove-resource-group-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="[リソース グループの削除] ボタンの位置を示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](./includes/create-table-dotnet/remove-resource-group-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="リソース グループを削除するための確認ダイアログを示すスクリーンショット。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してリソース グループを削除するには、削除するリソース グループの名前を指定した [az group delete](/cli/azure/group#az_group_delete) コマンドを使用します。  リソース グループを削除すると、そのリソース グループに含まれているすべての Azure リソースも削除されます。

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell を使用してリソース グループを削除するには、削除するリソース グループの名前を指定した [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用します。  リソース グループを削除すると、そのリソース グループに含まれているすべての Azure リソースも削除されます。

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [テーブル データを Table API にインポートする](table-import.md)
