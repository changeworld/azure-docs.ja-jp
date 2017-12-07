---
title: "Azure Cosmos DB における一意なキー | Microsoft Docs"
description: "Azure Cosmos DB データベースで一意なキーを使用する方法について説明します。"
services: cosmos-db
keywords: "一意キー制約, 一意キー制約の違反"
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b15d5041-22dd-491e-a8d5-a3d18fa6517d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: rafats
ms.openlocfilehash: 030d6d136c16946d6231b165b4b33d496bb7c818
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB における一意なキー

一意なキーを使用すると、開発者はデータベースにデータ整合性のレイヤーを追加できます。 コンテナーの作成時に一意キー ポリシーを作成すると、1 つまたは複数の値の一意性を[パーティション](partition-data.md)ごとに保証できます。 一意キー ポリシーを使用したコンテナーが作成されると、一意キー制約で指定されている値と重複する値を含む項目を新たに作成したり更新したりすることができなくなります。   

> [!NOTE]
> 一意なキーは、[.NET](documentdb-sdk-dotnet.md)、[.NET Core](documentdb-sdk-dotnet-core.md) DocumentDB (SQL) SDK、および [MongoDB API](mongodb-feature-support.md#unique-indexes) の最新バージョンでサポートされています。 現時点では、Table API および Graph API で一意なキーはサポートされていません。 
> 
>

## <a name="use-case"></a>ユース ケース

一例として、[ソーシャル アプリケーション](use-cases.md#web-and-mobile-applications)に関連付けられているユーザー データベースで、メール アドレスに対して一意キー ポリシーを適用していると、どんな利点があるかを見てみます。 ユーザーのメール アドレスを一意なキーにすると、それぞれのレコードは一意なメール アドレスを持つことが保証され、重複するメール アドレスを持つ新しいレコードを作成することはできません。 

同じメール アドレス (ただし姓と名、メール アドレスが異なる) を持つ複数のレコードをユーザーが作成できるようにするには、一意キー ポリシーに別のパスを追加します。 そうして単にメール アドレスを基にして一意なキーを作成するのではなく、姓と名およびメールを組み合わせた一意なキーを作成できます。 このケースでは 3 つのパスの組み合わせをそれぞれ一意にすることが可能なため、次のパス値を持つ項目をデータベースに含めることができます。 これらのレコード 1 つ 1 つが、一意キー ポリシーに適合します。  

**firstName、lastName、email の一意なキーとして有効な値**

|名|姓|電子メール アドレス|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

上記の表に掲載されている組み合わせのレコードをもう 1 つ書き込もうとすると、一意キー 制約を満たしていないことを示すエラーが返されます。 Azure Cosmos DB が返すエラーは、"指定された ID または名前のリソースは既に存在します" か、 または "指定された ID、名前または一意なインデックスのリソースは既に存在します" という内容です。 

## <a name="using-unique-keys"></a>一意なキーを使用する

コンテナーの作成時には一意なキーを定義して、パーティション キーのスコープに含める必要があります。 先述の例を基に作成すると、郵便番号を基にパーティションを分割する場合、各パーティションで重複するレコードをテーブルから取得できます。

一意なキーを使用するために既存のコンテナーを更新することはできません。

一意キー ポリシーを持つコンテナーを作成してしまうと、コンテナーを再作成しない限りはポリシーを変更できません。 既存のデータに一意なキーを実装したい場合は、新しいコンテナーを作成してから適切な移行ツールを使用し、そのデータを新しいコンテナーに移動します。 DocumentDB (SQL) コンテナーの場合は、[データ移行ツール](import-data.md)を使用します。 MongoDB コンテナーの場合は、[mongoimport.exe または mongorestore.exe](mongodb-migrate.md) を使用します。

一意なキーには、それぞれ最大 16 個のパス値 (たとえば /firstName、/lastName、/address/zipCode など) を含めることができます。 

一意キー ポリシーには、それぞれ最大 10 個の一意キー制約または一意なキーの組み合わせを含めることができます。 そうすると姓、名、メール アドレスを使用する先ほどの例では、制約が 1 つだけあり、含めうる 16 個のパスのうち 3 個が使用されています。 

コンテナーに一意キー ポリシーがある場合、項目の作成、更新および削除に対して請求される要求ユニットは少し高めになります。 

スパースな一意キーはサポートされていません。 一意なパスに不足している値がある場合、それらの値は一意性制約に含まれる特別な null 値として扱われます。

## <a name="documentdb-sql-api-sample"></a>DocumentDB (SQL) API のサンプル

次のコード サンプルでは、2 つの一意キー制約を持つ DocumentDB (SQL) コンテナーを新たに作成する方法を示します。 最初の制約は、先述の例で説明した firstName、lastName、email の制約です。 2 つめの制約は users address/zipCode です。 このパスを、次の一意キー ポリシーに使用する JSON ファイルのサンプルが、コード サンプルの後にあります。 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipCode" } },

                }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

サンプル JSON ドキュメント。

```json
{
    "id": "1",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": [
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipCode": 98012
        }
    ],
}
```
## <a name="mongodb-api-sample"></a>MongoDB API のサンプル

次のコマンドの例は、MongoDB API のユーザー コレクションの firstName、lastName、email フィールドに対して、一意なインデックスを作成する方法を示しています。 これにより、コレクション内のすべてのドキュメント間で、3 つすべてのフィールドの組み合わせが一意であることが保証されます。 MongoDB API のコレクションでは、一意なインデックスが作成されるのはコレクションの作成後、コレクションにデータが設定される前になります。

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```

## <a name="next-steps"></a>次のステップ

この記事では、データベース内の項目に一意なキーを作成する方法について学習しました。 初めてコンテナーを作成する場合、一意なキーとパーティション キーは依存関係にあるため、[Azure Cosmos DB でのデータのパーティション分割](partition-data.md)に関する記事をご確認ください。 


