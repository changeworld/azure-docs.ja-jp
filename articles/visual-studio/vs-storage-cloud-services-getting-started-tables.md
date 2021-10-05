---
title: Visual Studio を使用して Table Storage の利用を開始する (クラウド サービス)
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio のクラウド サービス プロジェクトで Azure Table ストレージの使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823088"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure Table ストレージと Visual Studio 接続済みサービスの概要 (クラウド サービス プロジェクト)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概要

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用してクラウド サービス プロジェクトで Azure ストレージ アカウントを作成または参照した後に、Visual Studio で Azure Table Storage の使用を開始する方法について説明します。 **接続済みサービスの追加** 操作によって、プロジェクト内の Azure Storage にアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルに、ストレージ アカウントの接続文字列が追加されます。

Azure Table Storage サービスを使用すると、大量の構造化データを格納できるようになります。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

最初に、ストレージ アカウント内にテーブルを作成する必要があります。 コードで Azure テーブルを作成する方法やテーブルとエンティティの基本的な操作について説明します (テーブル エンティティの追加、変更、読み取りなど)。 サンプルは C\# コードで記述され、[NET 用 Microsoft Azure Storage クライアント ライブラリ](/previous-versions/azure/dn261237(v=azure.100))を使用しています。

**注:** Azure ストレージへの呼び出しを実行する API の一部は非同期です。 詳細については、 [Async および Await を使用した非同期プログラミング](/previous-versions/hh191443(v=vs.140)) に関するページをご覧ください。 次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

* プログラムを使用してテーブルを操作する方法の詳細については、「 [.NET を使用して Azure Table Storage を使用する](../cosmos-db/tutorial-develop-table-dotnet.md) 」をご覧ください。
* Azure Storage の一般情報については、「 [ストレージのドキュメント](https://azure.microsoft.com/documentation/services/storage/) 」をご覧ください。
* Azure Cloud Services の一般情報については、「 [Cloud Services のドキュメント](https://azure.microsoft.com/documentation/services/cloud-services/) 」をご覧ください。
* ASP.NET アプリケーションのプログラミングについての詳細は、「 [ASP.NET](https://www.asp.net) 」をご覧ください。

## <a name="access-tables-in-code"></a>コードでテーブルにアクセスする
クラウド サービス プロジェクト内のテーブルにアクセスするには、Azure Table Storage にアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. C# ファイル冒頭の名前空間宣言に、次の **using** ステートメントが含まれていることを確認します。
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. **AzureStorageConnectionString** オブジェクトを取得し、**TableServiceClient** を作成します。これは、テーブルの作成や削除など、アカウントレベルの操作を実行するものです。
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > 上記のコードはすべて、以下の例に示すコードの前に使用してください。
   
3. ストレージ アカウント内のテーブル オブジェクトを参照する **TableServiceClient** オブジェクトを取得します。
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. 特定のテーブルとエンティティを参照する **TableClient** 参照オブジェクトを取得します。
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>コードでテーブルを作成する
Azure テーブルは、「コードでテーブルにアクセスする」セクションで説明したように、**TableClient** オブジェクトを取得した後、**CreateIfNotExistsAsync** へ呼び出しを追加するだけで作成できます。

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとし、姓をパーティション キーとして使用する **CustomerEntity** という名前のエンティティ クラスを定義します。

```csharp
public class CustomerEntity : ITableEntity
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

エンティティに関連する AddEntity 操作は、「コードでテーブルにアクセスする」で作成した **TableClient** オブジェクトを使用して行われます。 **AddEntity** メソッドは、実行する操作を表します。 次のコード例は、**TableClient** オブジェクトと **CustomerEntity** オブジェクトを作成する方法を示しています。 操作を準備するために、**AddEntity** によってユーザー エンティティがテーブルに挿入されます。

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作で複数のエンティティをテーブルに挿入できます。 次のコード例は、2 つのエンティティ オブジェクト ("Jeff Smith" と "Ben Smith") を作成し、AddRange メソッドを使用して **addEntitiesBatch** オブジェクトにそれらのエンティティ オブジェクトを追加した後、**TableClient.SubmitTransactionAsync** を呼び出して操作を開始します。

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティのクエリを実行する場合は、**Query** メソッドを使用します。 次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、**GetEntityAsync** メソッドを使用して、"Ben Smith" という名前のユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、**GetEntityAsync.Result** の戻り値は **CustomerEntity** オブジェクトです。 クエリでパーティション キーと行キーの両方を指定することが、**Table** service から単一のエンティティを取得するための最速の方法です。

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、検索して削除できます。 次のコードは、"Ben Smith" という名前のユーザー エンティティを検索し、見つかったら削除します。

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
