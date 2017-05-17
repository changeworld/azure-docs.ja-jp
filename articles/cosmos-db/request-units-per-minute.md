---
title: "Azure Cosmos DB: 1 分あたりの要求ユニット (RU/m) | Microsoft Docs"
description: "1 分あたりの要求ユニットを使用してコストを削減する方法について説明します。"
services: cosmosdb
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 878b7335fb5e09bc8704f7211cc6293ad6ea4bea
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Azure Cosmos DB の 1 分あたりの要求ユニット

Azure Cosmos DB は、高速で予測可能なパフォーマンスの実現を支援し、アプリケーションの規模の拡大に合わせてシームレスにスケールできるように設計されています。 Cosmos DB コンテナーのスループットは、秒単位と分単位 (RU/m) の 2 種類の粒度でプロビジョニングできます。 分単位の粒度のスループットをプロビジョニングする目的は、秒単位の粒度で発生するワークロードの想定外のスパイクに対処することにあります。 

この記事では、1 分あたりの要求ユニット (RU/m) のプロビジョニングのしくみについて、概要を紹介します。 RU/m のプロビジョニングの目標として想定しているのは、(特に、運用データに分析が必要な場合などの) 予想外のニーズやワークロードのスパイクに際して、パフォーマンスの予測可能性を高めるという点です。 つまり、お客様がプロビジョニングしたスループットをもっと消費し、安心して迅速にスケールできるようにすることを目指しているのです。

この記事を読むと、次の質問に回答できるようになります。

* 1 分あたりの要求ユニットのしくみはどのようなものか。
* 1 分あたりの要求ユニットと 1 秒あたりの要求ユニットの違いは何か。
* RU/m をプロビジョニングするにはどうすればよいか。
* どのようなシナリオであれば、1 分あたりの要求ユニットのプロビジョニングを検討する必要があるか。
* ポータルのメトリックを使ってコスト パフォーマンスを最適化するにはどうすればよいか。
* RU/m の予算を消費することができる要求の種類を定義できるか。

## <a name="provisioning-request-units-per-minute-rum"></a>1 分あたりの要求ユニット (RU/m) をプロビジョニングする

秒単位の粒度 (RU/s) で Azure Cosmos DB をプロビジョニングした場合には、わずかな待ち時間で要求に成功することが保証されます。ただし、スループットがその 1 秒間に対してプロビジョニングされた容量を超えないことが条件となります。 RU/m では粒度が分単位となるため、その 1 分間に要求が成功することが保証されます。 このため、容量ぎりぎりで動作しているシステムと比べてパフォーマンスの予測可能性が高く、計画を立てやすいという利点があります。

1 分あたりのプロビジョニングのしくみはシンプルです。

* RU/m は、RU/s に追加する形で時間単位で課金されます。 詳細については、Azure Cosmos DB の[価格のページ](https://aka.ms/acdbpricing)を参照してください。
* RU/m は、コレクション レベルで有効にすることができます。 有効化の操作は、各種の SDK (Node.js、Java、または .Net) のほか、ポータル (および MongoDB API ワークロード) からも可能です。
* RU/m が有効になっていると、RU/s が 100 件プロビジョニングされるごとに、RU/m が (10 倍の) 1,000 件プロビジョニングされます。
* 要求ユニットがプロビジョニングされた RU/m を消費するのは、その 1 秒間に対してプロビジョニングされた RU の数を超過した場合に限られます。
* 60 秒の期間 (UTC) が終わると、1 分あたりのプロビジョニングの数が補充され、元に戻ります。
* RU/m は、プロビジョニングされている RU/s がパーティションあたり 5,000 件までのコレクションに限り有効にすることができます。 スループットのニーズをスケールしていくうちにパーティションあたりのプロビジョニングがこの水準に達したときは、警告メッセージが表示されます。

以下に示したのは、具体的な例です。ここでは、お客様が RU/s を10,000 件、RU/m を 100,000 件プロビジョニングすることによって、RU/s が 10,000 件 (RU/m は 100,000 件) プロビジョニングされているコレクションで 90 秒間にピーク時 (1 秒あたり RU 50,000 件) に対応するためのプロビジョニングにかかるコストを 73% 削減しています。

* 1 秒目: RU/m の予算が 100,000 に設定されます。
* 3 秒目: 3 秒目の要求ユニットの消費量は、11,010 件でした。これは、プロビジョニングされている RU/s よりも 1,010 多い数字となっています。 このため、RU/m の予算から RU が 1,010 件差し引かれます。 RU/m の予算では、以後 57 秒間に RU をあと 98,990 件利用できます。
* 29 秒目: ここでは、大きなスパイク (1 秒間に対してプロビジョニングされている量の 4 倍) が発生したため、要求ユニットの消費量が 46,920 件になりました。 これに伴い、RU/m の予算から RU が 36,920 件差し引かれます。このため、28 秒目には 92,323 件であった RU は、29 秒目には残り 55,403 件となりました。
* 61 秒目: RU/m の予算が再び 100,000 に設定されます。
 
![Azure Cosmos DB の消費量とプロビジョニングの状況を示したグラフ](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>RU/m を使って要求ユニットの容量を指定する

Azure Cosmos DB コレクションの作成時には、そのコレクション用に予約する 1 秒あたりの要求ユニット数 (RU/s) を指定します。 これに加えて、1 分あたりの RU を追加するかどうかを決めることもできます。 この操作は、ポータルまたは SDK を使って実行します。 

### <a name="through-the-portal"></a>ポータルを使用する場合

1 分あたりの RU の有効または無効を設定する操作は、コレクションをプロビジョニングするときにボタンを 1 回クリックするだけで完了します。 

 ![Azure Portal で RU/m を設定する方法を示したスクリーンショット](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>SDK を使用する場合
まずは、RU/m が以下の SDK のみ利用可能であるという点にご注意ください。

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

次に示すコード スニペットは、.NET SDK を使用して、要求ユニット数が 1 秒あたりでは 3,000 件、1 分あたりでは 30,000 件のコレクションを作成するものです。

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

次に示すコード スニペットでは、.NET SDK を使用して、コレクションのスループットを 1 秒あたり RU 5,000 件 (1 分あたりの RU はプロビジョニングなし) に変更します。

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>使用が適したシナリオ

このセクションでは、1 分あたりの要求ユニットの使用が適していると考えられるシナリオを簡単に紹介します。

**開発/テスト環境:** 使用が適していると言えます。 開発中にさまざまなワークロードでアプリケーションをテストしているときには、RU/m を使うと柔軟性を高めることができます。 Azure Cosmos DB のテストに利用できるツールとしては無料の[エミュレーター](../documentdb/documentdb-nosql-local-emulator.md)も優秀ですが、 クラウド環境で作業を開始する場合には、RU/m を使えばその場限りのパフォーマンス ニーズに対応できる優れた柔軟性が得られます。 最初の時点でパフォーマンス面のニーズを心配する必要が少なくなり、これまでよりも多くの時間を開発に割けるようになります。 初めは RU/s のプロビジョニングを最小限にして RU/m を有効にすることをお勧めします。

**予測が困難で、スパイクがよく発生し、分単位の粒度が必要:** 使用が適しており、25 ～ 75% の節約が可能です。 RU/m では、これまでに大幅なコスト削減を実現してきました。ほとんどの運用環境のシナリオが、このグループに該当します。 IoT ワークロードで 1 分間にスパイクが数回発生する場合や、システムで一度に大量のデータを挿入するときにクエリを実行する場合には、ニーズのスパイクに対応するために追加の容量が必要になります。 以下で紹介する段階的アプローチを使って、リソースに対するニーズを最適化することをお勧めします。

 ![粒度を 5 分として要求の消費量を示したグラフ](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *図 - RU 消費量のベンチマーク*

**安心感:** 使用が適しており、10 ～ 20% の節約が可能です。 ピークやスロットルの心配をすることなく安心していたいだけの人もいるでしょう。 そのような人には、この機能がぴったりです。 この場合、RU/m を有効にしたうえで、1 秒あたりのプロビジョニング量をわずかに減らすことをお勧めします。 このシナリオは、プロビジョニングを積極的に最適化しようとしているわけではないという点で上の 2 つと異なります。 これは、"スロットル ゼロ" を目指す考え方と言えるでしょう。

臨時のニーズが発生する重要な操作: 場合によっては、重要な操作のみ RU/m の予算にアクセスできるようにして、臨時の操作や比較的重要性の低い操作によって予算が消費されることのないようにする方法をお勧めします。 これは、以下のセクションに示した方法で簡単に定義することができます。

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>ポータルのメトリックを使ってコスト パフォーマンスを最適化する

**今後数週間の間に、1 分あたりの RU の消費量を監視してスループットのニーズを最適化することに関してコンテンツを追加する予定です。**

ポータルのメトリックでは、通常の 1 秒あたりの RU 消費量と、1 分あたりの RU 消費量を比較しながら確認できます。 ポータルのメトリックを監視することは、プロビジョニングの最適化に役立ちます。 

最適な RU/m の使い方を検討するにあたっては、以下の段階的アプローチの使用をお勧めしています。 それぞれの手順で、ワークロードの (時間、日、または週単位の) サイクル全体の RU の消費量について概要を把握し、プロビジョニングした RU の使用状況に関する洞察を得ることができます。

このアプローチの原則は、スループットのプロビジョニングを以下のパフォーマンス基準に合致するプロビジョニング ポイントに可能な限り近づけるというものです。 

![粒度を 5 分として要求の消費量を示したグラフ](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
自分のワークロードに最適なプロビジョニング ポイントを知るには、以下を把握する必要があります。

* 消費パターン: スパイクの発生はゼロか、頻繁でないか、それとも継続的か。 また、スパイクの大きさは小規模 (平均の 2 倍)、中規模、大規模 (平均の 10 倍超) のどれか。
* スロットルの対象となる要求の割合: スロットルが多少発生しても問題がないか。 問題がない場合には、どの程度までなら耐えられるか。 

自分の目標が明らかになったら、最適なプロビジョニングに近づけていきます。

ここでは、RU/m の消費量に基づいてプロビジョニングを最適化する方法に関して全般的なガイダンスを紹介します。 このガイダンスは、非公開のプレビューで得られた知見に基づくものであり、あらゆるワークロードに当てはまるわけではありません。 新たなデータに基づいてベースラインを変更することもありますのでご注意ください。

|RU/m の使用率|RU/m の使用率の程度|プロビジョニングに関して推奨される対策|
|---|---|---|
|0 ～ 1%|使用率が低い|RU/m の消費量が増えるよう、RU/s を減らす|
|1 ～ 10%|使用量は正常|このプロビジョニング レベルを維持する|
|10% 超|使用率が高い|RU/s を増やして RU/m に対する依存を減らす|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>RU/m の予算を消費できる操作を選択する

操作の種類に関係なく要求レベルでも、RU/m の予算を消費できるかどうかを設定できます。 RU/s の通常の予算がすべて消費されていた場合に、要求が RU/m の予算を消費できない場合には、その要求はスロットルされます。 既定では、RU/m のスループットの予算が有効になっている場合、あらゆる要求が RU/m の予算を消費できます。 

DocumentDB API for CRUD とクエリ操作を使用して RU/m の予算を無効にするためのコード スニペットは、以下のとおりです。

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Cosmos DB でのパーティション分割のしくみ、パーティション分割コレクションの作成方法、またアプリケーションに最適なパーティション キーの選択方法を説明しました。

* Azure Cosmos DB のスケールとパフォーマンスのテストを行う。 サンプルについては、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](../documentdb/documentdb-performance-testing.md)」を参照してください。
* [SDK](../documentdb/documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使ってコーディングを開始します。
* Azure Cosmos DB における[スループットのプロビジョニング](../documentdb/documentdb-request-units.md)について理解します。 


