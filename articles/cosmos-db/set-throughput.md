---
title: Azure Cosmos DB のスループットのプロビジョニング | Microsoft Docs
description: Azure Cosmos DB コンテナー、コレクション、グラフ、およびテーブルのプロビジョニング済みスループットを設定する方法について説明します。
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Azure Cosmos DB コンテナーのスループットの設定および取得

Azure Portal またはクライアントの SDK を使用して、Azure Cosmos DB コンテナーのスループットを設定できます。 

次の表に、コンテナーで使用できるスループットを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コンテナー</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コンテナー</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>1,000 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal を使用してスループットを設定するには

1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com) を開きます。
2. 左側のバーで、**[Azure Cosmos DB]** をクリックするか、または下部にある **[すべてのサービス]** をクリックし、**[データベース]** までスクロールして **[Azure Cosmos DB]** をクリックします。
3. Azure Cosmos DB アカウントを選択します。
4. 新しいウィンドウで、ナビゲーション メニューの **[データ エクスプローラー]** をクリックします。
5. 新しいウィンドウで、データベースとコンテナーを展開し、**[Scale & Settings]\(スケールと設定\)** をクリックします。
6. 新しいウィンドウで、**[スループット]** ボックスに新しいスループット値を入力し、**[保存]** をクリックします。

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>SQL API for .NET を使用してスループットを設定するには

次のコード スニペットでは現在のスループットを取得し、それを 500 RU/秒に変更します。 完全なコード サンプルについては、GitHub の [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) プロジェクトを参照してください。

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>SQL API for Java を使用してスループットを設定するには

次のコード スニペットでは現在のスループットを取得し、それを 500 RU/秒に変更します。 完全なコード サンプルについては、GitHub の [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) ファイルを参照してください。 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>MongoDB API の GetLastRequestStatistics コマンドの使用によるスループットの取得

MongoDB API は、指定した操作の要求の使用量を取得するためのカスタム コマンド *getLastRequestStatistics* をサポートしています。

たとえば、Mongo シェルで、要求の使用量を確認する操作を実行します。
```
> db.sample.find()
```

次に、*getLastRequestStatistics* コマンドを実行します。
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

これを踏まえて、アプリケーションに必要な予約済みスループットの量を推定するには、典型的な操作の実行に関連する要求ユニット使用量を記録し、アプリケーションが使用する代表的なアイテムに基づいて、1 秒ごとに実行される操作数を推定します。

> [!NOTE]
> アイテムの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、典型的なアイテムの "*種類*" ごとに、適用可能な操作の要求ユニット使用量を記録してください。
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>MongoDB API ポータルのメトリックを使用したスループットの取得

MongoDB API データベースの要求ユニットの適切な推定使用量を取得する簡単な方法は、[Azure Portal](https://portal.azure.com) のメトリックを使用することです。 "*要求数*" のグラフと "*要求の使用量*" のグラフから、各操作が使用している要求単位の数と、互いに使用する要求単位の数を推定できます。

![MongoDB API ポータルのメトリック][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> MongoDB API での予約されたスループット上限の超過
コンテナーのプロビジョニング済みスループットを超えたアプリケーションは、使用量レートがプロビジョニング済みスループット レートを下回るまでレート制限されます。 レート制限が発生すると、バックエンドは、`16500`エラー コード - `Too Many Requests` で機先を制して要求を終了します。 既定では、MongoDB API は、`Too Many Requests`エラー コードを返す前に、最大 10 回の再試行を自動的に実行します。 `Too Many Requests`エラー コードが多数発生する場合は、アプリケーションのエラー処理ルーチンに再試行ロジックを追加するか、[コンテナーのプロビジョニング済みスループットを増やすことを検討した方がよいことがあります](set-throughput.md)。

## <a name="throughput-faq"></a>スループットについてよく寄せられる質問

**スループットを 400 RU/秒未満に設定することはできますか?**

Cosmos DB のシングル パーティション コンテナーで使用できる最小スループットは 400 RU/秒です (パーティション分割コンテナーの最小スループットは 1,000 RU/秒)。 要求ユニットは 100 RU/秒の間隔で設定されますが、スループットを 100 RU/秒または 400 RU/秒未満の値に設定することはできません。 コストを抑えて Cosmos DB の開発とテストを行いたい場合は、無料の [Azure Cosmos DB Emulator](local-emulator.md) を利用して無償でローカルにデプロイすることができます。 

**MongoDB API を使用してスループットを設定する方法**

スループットを設定するための MongoDB API 拡張機能はありません。 「[SQL API for .NET を使用してスループットを設定するには](#set-throughput-sdk)」で示すように、SQL API を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

Cosmos DB を使用したプロビジョニングと地球規模での使用の詳細については、[Cosmos DB でのパーティション分割とスケーリング](partition-data.md)に関するページをご覧ください。

[1]: ./media/set-throughput/api-for-mongodb-metrics.png