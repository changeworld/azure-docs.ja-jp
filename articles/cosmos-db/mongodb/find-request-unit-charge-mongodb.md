---
title: MongoDB 操作のための Azure Cosmos DB API の要求ユニット使用量を確認する
description: Azure Cosmos コンテナーに対して実行した MongoDB クエリの要求ユニット (RU) 使用量を確認する方法について説明します。 Azure portal、MongoDB .NET、Java、Node.js のドライバーを使用できます。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.custom: devx-track-js
ms.openlocfilehash: c2ad6ef764fa13a6876f1727314d5a7944ce7d31
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035217"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API で実行された操作の要求ユニット使用量を確認する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリにまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求の料金は、すべてのデータベース操作で使用される要求ユニットです。 RU は、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨と考えることができます。 Azure Cosmos コンテナーの操作にどの API 使用するかに関係なく、コストは RU によって測定されます。 データベース操作が書き込み、ポイント読み取り、またはクエリのいずれの場合でも、コストは常に RU で測定されます。 詳細については、[要求ユニットとその考慮事項](../request-units.md)に関する記事を参照してください。

この記事では、MongoDB 用 Azure Cosmos DB API でコンテナーに対して実行された任意の操作の[要求ユニット](../request-units.md) (RU) 消費量を確認できる、さまざまな方法を紹介します。 異なる API を使用している場合は、[SQL API](../find-request-unit-charge.md)、[Cassandra API](../cassandra/find-request-unit-charge-cassandra.md)、[Gremlin API](../find-request-unit-charge-gremlin.md)、[Table API](../table/find-request-unit-charge.md) に関する記事を参照して RU 消費量を確認してください。

RU 使用量は、`getLastRequestStatistics` という名前のカスタム [データベース コマンド](https://docs.mongodb.com/manual/reference/command/)によって公開されます。 最後に実行された操作の名前を含んだドキュメント、その要求の使用量、その実行時間が、このコマンドから返されます。 Azure Cosmos DB API for MongoDB を使用している場合、RU 使用量をいくつかの方法で取得できます。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成](create-mongodb-dotnet.md#create-a-database-account)してデータを取り込むか、既にデータが存在する既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウに進み、操作の対象となるコンテナーを選択します。

1. コンテナー名の横にある **[...]** を選択し、 **[新しいクエリ]** を選択します。

1. 有効なクエリを入力し、 **[クエリの実行]** を選択します。

1. **[Query Stats]\(クエリの統計\)** を選択すると、実行した要求の実際の使用量が表示されます。 このクエリ エディターでは、クエリ述語に対してのみ要求ユニットの課金を実行し、表示できます。 このエディターは、挿入ステートメントなどのデータ操作コマンドには使用できません。

   :::image type="content" source="../media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure portal で MongoDB クエリの要求の使用量を表示した画面のスクリーンショット":::

1. データ操作コマンドの要求料金を取得するには、`getLastRequestStatistics` コマンドを Mongo シェル、[Robo 3T](connect-using-robomongo.md)、[MongoDB コンパス](connect-using-compass.md)、シェル スクリプトを使用した Visual Studio Code 拡張機能などのシェル ベース UI から実行します。

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>MongoDB .NET ドライバーの使用

[公式の MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)を使用するとき、`RunCommand` メソッドを `IMongoDatabase` オブジェクトで呼び出すことでコマンドを実行できます。 このメソッドには、`Command<>` 抽象クラスの実装が必要となります。

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

詳細については、「[クイック スタート: Azure Cosmos DB の MongoDB 用 API を使用して .NET Web アプリを構築する](create-mongodb-dotnet.md)」を参照してください。

## <a name="use-the-mongodb-java-driver"></a>MongoDB Java ドライバーの使用


[公式の MongoDB Java ドライバー](https://mongodb.github.io/mongo-java-driver/)を使用するとき、`runCommand` メソッドを `MongoDatabase` オブジェクトで呼び出すことでコマンドを実行できます。

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

詳細については、「[クイック スタート: Azure Cosmos DB の MongoDB 用 API と Java SDK を使用して Web アプリを構築する](create-mongodb-java.md)」を参照してください。

## <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js ドライバーの使用

[公式の MongoDB Node.js ドライバー](https://mongodb.github.io/node-mongodb-native/)を使用するとき、`command` メソッドを `db` オブジェクトで呼び出すことでコマンドを実行できます。

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

詳細については、「[クイック スタート: 既存の MongoDB Node.js Web アプリを Azure Cosmos DB に移行する](create-mongodb-nodejs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](../request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](../optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](../optimize-cost-reads-writes.md)
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください