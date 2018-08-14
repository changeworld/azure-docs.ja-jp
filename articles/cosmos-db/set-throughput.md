---
title: Azure Cosmos DB のスループットのプロビジョニング | Microsoft Docs
description: Azure Cosmos DB コンテナー、コレクション、グラフ、およびテーブルのプロビジョニング済みスループットを設定する方法について説明します。
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: andrl
ms.openlocfilehash: 2c3e4806aef506ef9016699b46eadd5f8a187224
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038453"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Azure Cosmos DB コンテナーおよびデータベースのスループットを設定および取得する

Azure Portal またはクライアント SDK を使用して、Azure Cosmos DB コンテナーまたは一連のコンテナーのスループットを設定できます。 

**個々のコンテナーのスループットをプロビジョニングする:** 一連のコンテナーのスループットをプロビジョニングすると、これらすべてのコンテナーは、プロビジョニングされたスループットを共有します。 各コンテナーのスループットを個別にプロビジョニングすると、その特定のコンテナーのスループットが確実に予約されます。 コンテナーは、RU/秒を個別のコンテナー レベルで割り当てるときに、"*固定*" または "*無制限*" として作成できます。 固定サイズのコンテナーの上限は、容量が 10 GB で、スループットが毎秒 10,000 RU となります。 無制限のコンテナーを作成する場合は、少なくとも 1,000 RU/秒のスループットと[パーティション キー](partition-data.md)を指定する必要があります。 データが複数のパーティションに分割される場合があるため、高カーディナリティ (100 ～数百万の個別の値) のパーティション キーを選択する必要があります。 多数の異なる値を持つパーティション キーを選択すると、コンテナー/テーブル/グラフおよび要求を、Azure Cosmos DB で確実かつ一様に拡大縮小できるようになります。 

**一連のコンテナーまたはデータベースのスループットをプロビジョニングする:** データベースのスループットをプロビジョニングすると、そのデータベースに属するすべてのコンテナー間でスループットを共有できます。 Azure Cosmos DB データベースでは、スループットを一連のコンテナー間で共有することも、各コンテナーに個別に専用のスループットを設定することもできます。 一連のコンテナーに RU/秒を割り当てるとき、そのセットに属するコンテナーは "*無制限*" コンテナーとして処理され、パーティション キーを指定する必要があります。

プロビジョニング済みのスループットに基づいて、Azure Cosmos DB はお使いのコンテナーをホストする物理パーティションを割り当て、データの増加に応じてパーティション間でデータの分割やバランスの再調整を行います。 コンテナー レベルとデータベース レベルのスループットのプロビジョニングは別個のサービスであり、これらのサービス間で切り替えを行うには移行元から移行先へのデータの移行が必要になります。 つまり、新しいデータベースまたは新しいコレクションを作成した後、[Bulk Executor ライブラリ](bulk-executor-overview.md)または [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用してデータを移行する必要があります。 次の図は、さまざまなレベルでのスループットのプロビジョニングを示しています。

![個別のコンテナーおよび一連のコンテナーに対する要求ユニットのプロビジョニング](./media/request-units/provisioning_set_containers.png)

以降のセクションでは、Azure Cosmos DB アカウントにさまざまなレベルのスループットを構成するために必要な手順について説明します。 

## <a name="provision-throughput-by-using-azure-portal"></a>Azure Portal を使用してスループットをプロビジョニングする

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>コンテナー (コレクション/グラフ/テーブル) にスループットをプロビジョニングする

1. [Azure Portal](https://portal.azure.com) にサインインします。  
2. 左側のナビゲーションから **[All resources]\(すべてのリソース\)** を選択し、Azure Cosmos DB アカウントを検索します。  
3. コンテナー (コレクション、グラフ、テーブル) の作成中にスループットを設定したり、または既存のコンテナーのスループットを更新することもできます。  
4. コンテナーの作成中にスループットを割り当てるには、**データ エクスプローラー** ブレードを開き、**[New Collection]\(新しいコレクション\)** (その他の API の場合は [New Graph]\(新しいグラフ\)、[New Table]\(新しいテーブル\)) を選択します。  
5. **[コレクションの追加]** ブレード のフォームに入力します。 このブレードのフィールドについては、次の表で説明します。  

   |**設定**  |**説明**  |
   |---------|---------|
   |データベース ID  |  データベースを識別する一意な名前を指定します。 データベースは、1 つまたは複数のコレクションから成る論理的なコンテナーです。 データベース名は 1 文字以上 255 文字以内にする必要があります。/、\\、#、? は使えず、末尾にスペースを入れることもできません。 |
   |コレクション ID  | コレクションを識別する一意な名前を指定します。 コレクション ID には、データベース名と同じ文字要件があります。 |
   |ストレージの容量   | この値は、データベースの記憶域容量です。 各コレクションのスループットをプロビジョニングする場合、容量には、**固定 (10 GB)** または**無制限**を設定できます。 無制限のストレージ容量の場合、データのパーティション キーを設定する必要があります。  |
   |Throughput   | 各コレクションおよびデータベースには、1 秒あたりの要求単位でスループットを設定できます。  固定のストレージ容量の場合、最小スループットは秒あたり 400 要求単位 (RU/秒)、無制限のストレージの容量の場合、最小スループットは 1000 RU/秒に設定されます。|

6. これらのフィールドに値を入力した後、**[OK]** を選択して設定を保存します。  

   ![コレクションのスループットを設定する](./media/set-throughput/set-throughput-for-container.png)

7. 既存のコンテナーのスループットを更新するには、データベースとコンテナーを展開し、**[設定]** をクリックします。 新しいウィンドウに、新しいスループットの値を入力し、**[保存]** を選択します。  

   ![コレクションのスループットを更新する](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>一連のコンテナーまたはデータベース レベルのスループットをプロビジョニングする

1. [Azure Portal](https://portal.azure.com) にサインインします。  
2. 左側のナビゲーションから **[All resources]\(すべてのリソース\)** を選択し、Azure Cosmos DB アカウントを検索します。  
3. スループットは、データベースの作成中に設定したり、または既存のコンテナーのスループットを更新できます。  
4. データベースの作成中にスループットを割り当てるには、**データ エクスプローラー** ブレードを開き、**[新しいデータベース]** を選択します。  
5. **[データベース ID]** 値を入力し、**[スループットのプロビジョニング]** オプションをオンにし、スループットの値を設定します。 データベースには、50,000 RU/秒の最小スループット値をプロビジョニングできます。  

   ![新しいデータベース オプションでスループットを設定する](./media/set-throughput/set-throughput-with-new-database-option.png)

6. 既存のデータベースのスループットを更新するには、データベースとコンテナーを展開し、**[スケール]** をクリックします。 新しいウィンドウに、新しいスループットの値を入力し、**[保存]** を選択します。  

   ![データベースのスループットを更新する](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>データベース内の個別のコンテナーおよび一連のコンテナーのスループットをプロビジョニングする

1. [Azure Portal](https://portal.azure.com) にサインインします。  
2. 左側のナビゲーションから **[All resources]\(すべてのリソース\)** を選択し、Azure Cosmos DB アカウントを検索します。  
3. データベースを作成し、スループットを割り当てます。 **データ エクスプローラー** ブレードを開き、**[新しいデータベース]** を選択します。  
4. **[データベース ID]** 値を入力し、**[スループットのプロビジョニング]** オプションをオンにし、スループットの値を設定します。 データベースには、50,000 RU/秒の最小スループット値をプロビジョニングできます。  

   ![新しいデータベース オプションでスループットを設定する](./media/set-throughput/set-throughput-with-new-database-option.png)

5. 次に、上記の手順で作成したデータベース内にコレクションを作成します。 コレクションを作成するには、データベースを右クリックして **[新しいコレクション]** を選択します。  

6. **[コレクションの追加]** ブレードに、コレクションの名前とパーティション キーを入力します。 または、特定のコンテナーにスループット値を割り当てない場合は、そのコンテナーのスループットをプロビジョニングし、データベースに割り当てられたスループットをコレクションで共有できます。  

   ![または、そのコンテナーのスループットを設定します。](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>スループットをプロビジョニングするときの考慮事項

スループット予約戦略を決定する際に役立ついくつかの考慮事項を以下に示します。

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>データベース レベルのスループットをプロビジョニングするときの考慮事項

次の場合は、データベース レベル (一連のコンテナー) でスループットをプロビジョニングすることを検討します。

* 一部またはすべてのコンテナー間でスループットを共有する可能性があるコンテナーの数が 12 個以上の場合。  

* IaaS でホストされた VM またはオンプレミス (NoSQL またはリレーショナル データベースなど) 上で実行するよう設計されたシングル テナント データベースから Azure Cosmos DB に移行する場合。  

* データベース レベルでプールされたスループットを使用して、ワークロードの計画外の急激な増加に対応することを検討している場合。  

* コンテナーごとに個別にスループットを設定するのではなく、データベース内の一連のコンテナー全体の集計スループットを取得することに関心がある場合。

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>コンテナー レベルのスループットをプロビジョニングするときの考慮事項

次の場合は、各コンテナーのスループットを個別にプロビジョニングすることを検討してください。

* Azure Cosmos DB コンテナーの数が少ない場合。  

* SLA によって保証された特定の コンテナーのスループットを取得する必要がある場合。

## <a name="throughput-ranges"></a>スループット範囲

次の表に、コンテナーで使用できるスループットを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コンテナー</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コンテナー</strong></p></td>
            <td valign="top"><p><strong>一連のコンテナー</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>1,000 要求ユニット/秒</p></td>
            <td valign="top"><p>50,000 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>無制限</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>SQL API for .NET を使用してスループットを設定する

### <a name="set-throughput-at-the-container-level"></a>コンテナー レベルでスループットを設定する
次に示すコード スニペットでは、SQL API の .NET SDK を使用して、個別のコンテナーに対して 1 秒あたり 3,000 要求ユニットのコンテナーを作成します。

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>データベース レベルで一連のコンテナーのスループットを設定する

次に示すコード スニペットでは、SQL API の .NET SDK を使用して、一連のコンテナーに対して 1 秒あたり 100,000 要求ユニットをプロビジョニングします。

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = await client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 });

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB は、スループットの予約モデルで運用されます。 つまり、実際に "*使用した*" スループット量ではなく、"*予約した*" スループット量に対する料金が請求されます。 アプリケーションの負荷やデータ、使用パターンが変化したときは、SDK や [Azure Portal](https://portal.azure.com) を使用して、予約済み RU の数を簡単にスケールアップしたりスケールダウンしたりできます。

各コンテナーまたは一連のコンテナーは、Azure Cosmos DB の `Offer` リソースにマップされます。このリソースは、プロビジョニング済みスループットに関するメタデータを持っています。 割り当て済みのスループットを変更するには、コンテナーに対応する Offer リソースを検索して、新しいスループット値に更新します。 次に示すコード スニペットは、.NET SDK を使用して、コンテナーのスループットを 1 秒あたり 5,000 要求ユニットに変更します。 スループットを変更した後は、変更されたスループットが表示されるように既存の Azure portal ウィンドウを更新する必要があります。 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

スループットを変更しても、お使いの 1 つのコンテナーまたは一連のコンテナーの可用性には影響しません。 通常、新しく予約されたスループットは、数秒後にアプリケーションで有効になります。

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>SQL API for Java を使用してスループットを設定するには

次のコード スニペットでは現在のスループットを取得し、それを 500 RU/秒に変更します。 完全なコード サンプルについては、GitHub の [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) ファイルを参照してください。 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
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

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>MongoDB API ポータルのメトリックを使用したスループットの取得

MongoDB API データベースの要求ユニットの適切な推定使用量を取得する簡単な方法は、[Azure Portal](https://portal.azure.com) のメトリックを使用することです。 "*要求数*" のグラフと "*要求の使用量*" のグラフから、各操作が使用している要求単位の数と、互いに使用する要求単位の数を推定できます。

![MongoDB API ポータルのメトリック][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> MongoDB API での予約されたスループット上限の超過
1 つのコンテナーまたは一連のコンテナーのプロビジョニング済みスループットを超えたアプリケーションは、使用量レートがプロビジョニング済みスループット レートを下回るまでレート制限されます。 レート制限が発生すると、バックエンドは、`16500`エラー コード - `Too Many Requests` で要求を終了します。 既定では、MongoDB API は、`Too Many Requests`エラー コードを返す前に、最大 10 回の再試行を自動的に実行します。 `Too Many Requests`エラー コードが多数発生する場合は、アプリケーションのエラー処理ルーチンに再試行ロジックを追加するか、[コンテナーのプロビジョニング済みスループットを増やすことを検討した方がよいことがあります](set-throughput.md)。

## <a id="GetLastRequestStatistics"></a>MongoDB API の GetLastRequestStatistics コマンドの使用による要求の使用量を取得する

MongoDB API は、指定した操作の要求の使用量を取得するためのカスタム コマンド *getLastRequestStatistics* をサポートしています。

たとえば、Mongo シェルで、要求の使用料を確認する操作を実行します。
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

アプリケーションに必要な予約済みスループットの量を推定するには、典型的な操作の実行に関連する要求ユニット使用量を記録し、アプリケーションが使用する代表的なアイテムに基づいて、1 秒ごとに実行される操作数を推定します。

> [!NOTE]
> アイテムの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、典型的なアイテムの "*種類*" ごとに、適用可能な操作の要求ユニット使用量を記録してください。
> 
> 

## <a name="throughput-faq"></a>スループットについてよく寄せられる質問

**スループットを 400 RU/秒未満に設定することはできますか?**

Cosmos DB のシングル パーティション コンテナーで使用できる最小スループットは 400 RU/秒です (パーティション分割コンテナーの最小スループットは 1,000 RU/秒)。 要求ユニットは 100 RU/秒の間隔で設定されますが、スループットを 100 RU/秒または 400 RU/秒未満の値に設定することはできません。 コストを抑えて Cosmos DB の開発とテストを行いたい場合は、無料の [Azure Cosmos DB Emulator](local-emulator.md) を利用して無償でローカルにデプロイすることができます。 

**MongoDB API を使用してスループットを設定する方法**

スループットを設定するための MongoDB API 拡張機能はありません。 「[SQL API for .NET を使用してスループットを設定するには](#set-throughput-sdk)」で示すように、SQL API を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

* スループットおよび要求単位の見積もりの詳細については、「[要求ユニットとスループットの推定 - Azure Cosmos DB](request-units.md)」を参照してください。

* Cosmos DB を使用したプロビジョニングと地球規模での使用の詳細については、[Cosmos DB でのパーティション分割とスケーリング](partition-data.md)に関するページをご覧ください。

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
