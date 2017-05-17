---
title: "Azure Cosmos DB Graph API の概要 | Microsoft Docs"
description: "Azure Cosmos DB を使用してクエリを格納し、Gremlin クエリ言語を使用して低待機時間で大規模なグラフをトラバースする方法について説明します。"
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Azure Cosmos DB の概要: Graph API

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル配布](../documentdb/documentdb-distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性レベル](../documentdb/documentdb-consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 

![Gremlin、グラフ、Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB は、ターン キー グローバル配布、ストレージとスループットのエラスティック スケーリング、99 パーセンタイルの低待機時間 (読み取り時 10 ms 未満、書き込み時 15 ms 未満)、自動インデックス作成とクエリ、調整可能な一貫性レベル、99.99% の可用性を含む包括的な SLA に加え、グラフ モデリングおよびトラバーサル API も提供します。 Azure Cosmos DB は、[Gremlin 標準](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)を使用して照会することができ、Gremlin 互換の他のグラフ システムと統合されます。

この記事では、Azure Cosmos DB の Graph API の概要を説明します。また、Azure Cosmos DB を使用して、数十億の頂点と辺が含まれた大規模なグラフを格納し、数ミリ秒オーダーの待機時間でそれらのグラフを照会する方法、グラフ構造とスキーマを簡単に進化させる方法についても説明します。 

## <a name="graph-databases"></a>グラフ データベース
実世界で出現するデータは、自然に結び付けられています。 従来のデータ モデリングはエンティティに重点を置いています。 しかし、多くのアプリケーションでは、エンティティ間の豊富なリレーションシップもモデル化する必要があります。 グラフを使用すると、エンティティとリレーションシップの両方を自然にモデル化できます。 

[グラフ](http://mathworld.wolfram.com/Graph.html)とは、[頂点](http://mathworld.wolfram.com/GraphVertex.html)と[辺](http://mathworld.wolfram.com/GraphEdge.html)で構成された構造です。 頂点と辺は、いずれも任意の数のプロパティを持つことができます。 頂点は、ユーザー、場所、イベントなどの個々のオブジェクトを表します。 辺は、頂点間のリレーションシップを表します。 たとえば、ユーザーが別のユーザーを知っている可能性があること、イベントに関与していること、特定の場所に最近いたことなどが示されます。 プロパティは頂点と辺に関する情報を表します。 プロパティの例として、頂点に含まれる名前や年齢、辺に含まれるタイムスタンプや重みなどがあります。 正式には、このモデルは[プロパティ グラフ](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model)と呼ばれます。 Azure Cosmos DB は、プロパティ グラフ モデルをサポートしています。 

たとえば、次の図のサンプル グラフは、ユーザー、モバイル デバイス、関心事、オペレーティング システムの間のリレーションシップを示しています。 

![ユーザー、デバイス、関心事を示すサンプル データベース](./media/graph-introduction/sample-graph.png) 

グラフは、科学、テクノロジ、ビジネスにおけるさまざまなデータセットを理解するうえで役立ちます。 グラフ データベースにより、グラフを効率的かつ自然にモデル化して格納できるので、さまざまなシナリオでグラフが魅力的なものになります。 これらのユース ケースでは、スキーマの柔軟性と迅速な反復処理も必要であることが多いため、通常、グラフ データベースは NoSQL データベースです。 

グラフは斬新で強力なデータ モデリング手法です。 ただし、それだけでは、グラフ データベースを使用する十分な理由にはなりません。 グラフ トラバーサルを必要とする多くのユース ケースやパターンでは、グラフは従来の SQL データベースや NoSQL データベースよりもパフォーマンスがはるかに優れています。 このパフォーマンスの違いは、友人の友人のような複数のリレーションシップをトラバースするときにさらに増幅されます。 

グラフ データベースが提供する高速トラバーサルと、深さ優先探索、幅優先探索、ダイクストラ法などのグラフ アルゴリズムを組み合わせることで、ソーシャル ネットワーク、地理空間、コンテンツ管理、レコメンデーションなどのさまざまな分野で問題を解決できます。 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Azure Cosmos DB による地球規模のグラフ
Azure Cosmos DB は、グローバル配布、ストレージとスループットのエラスティック スケーリング、自動インデックス作成とクエリ、調整可能な整合性レベルを提供し、TinkerPop 標準をサポートする、完全に管理されたグラフ データベースです。  

![Azure Cosmos DB グラフ アーキテクチャ](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB は、市場の他のグラフ データベースと差別化する次の機能を備えています。 

* **弾力的にスケール可能なスループットとストレージ**: 実際のグラフは、1 つのサーバーの容量を超えてスケールする必要があります。 Azure Cosmos DB では、グラフを複数のサーバー間でシームレスにスケールできます。 また、アクセス パターンに基づいて、グラフのスループットを個別にスケールすることもできます。 Azure Cosmos DB は、実質的に無制限のストレージ サイズとプロビジョニング スループットにスケールできるグラフ データベースをサポートしています。 

* **複数リージョンのレプリケーション**: Azure Cosmos DB は、アカウントに関連付けられているすべてのリージョンにグラフ データを透過的にレプリケートします。これにより、整合性、可用性、パフォーマンスを所定のレベルで確保して、これらのトレードオフを実現しながら、データへのグローバル アクセスを必要とするアプリケーションを開発できます。 Azure Cosmos DB は、マルチホーミング API を使用した透過的なリージョン内フェールオーバーを提供します。また、スループットとストレージを世界規模で弾力的にスケールすることもできます。

* **使いやすい Gremlin 構文を使用した高速のクエリとトラバーサル**: 異種の頂点と辺を格納し、使いやすい Gremlin 構文を使用してこれらのドキュメントを照会できます (強化された SQL も間もなく提供される予定です)。 Azure Cosmos DB では、同時実行性の高い、ロックのないログ構造のインデックス作成技術を利用して、すべてのコンテンツのインデックスを自動的に作成します。 そのため、スキーマのヒント、セカンダリ インデックス、またはビューを指定しなくても、豊富なリアルタイム クエリとトラバーサルが可能になります。 詳細については、[Gremlin を使用したグラフの照会](gremlin-support.md)に関する記事をご覧ください。

* **完全管理**: データベース リソースやコンピューター リソースの管理の必要性を排除します。 Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、スケールの管理、複雑なデータ層のアップグレードへの対応は不要です。 すべてのグラフが自動的にバックアップされ、リージョンの障害から保護されます。 Azure Cosmos DB アカウントを簡単に追加し、必要に応じて容量をプロビジョニングできるので、データベースの運用と管理ではなく、アプリケーションに注力できます。

* **自動インデックス作成:** 既定では、Azure Cosmos DB はグラフのノードおよび辺内のすべてのプロパティのインデックスを自動的に作成するので、スキーマや、セカンダリ インデックスの作成は不要です。 

* **Gremlin との互換性**: Azure Cosmos DB は、オープン ソースの Gremlin 標準をネイティブでサポートしており、他の Gremlin 対応グラフ システムと統合できます。 そのため、Titan や Neo4j などの別の異なるグラフ データベースから簡単に移行することができます。また、Apache Spark GraphX などのグラフ分析フレームワークで Azure Cosmos DB を使用することもできます。

* **調整可能な整合性レベル:** 整合性とパフォーマンスの最適なトレードオフを実現するために、明確に定義された 5 つの整合性レベルの中から選択できます。 Azure Cosmos DB では、クエリと読み取り操作に関して、強固、有界整合性制約、セッション、一貫性のあるプレフィックス、最終的の 5 種類の整合性レベルを提供します。 明確に定義されたきめ細かな整合性レベルにより、整合性、可用性、待機時間の適切なトレードオフを行うことができます。 詳細については、「 [一貫性レベルを使用した DocumentDB の可用性とパフォーマンスの最大化](../documentdb/documentdb-consistency-levels.md)」を参照してください。

Azure Cosmos DB では、同じコンテナーやデータベース内でドキュメントやグラフなどの複数のモデルを使用することもできます。 ドキュメント コレクションを使用してグラフ データをドキュメントと共に格納し、JSON に対する SQL クエリと Gremlin クエリの両方を使用して、同じデータをグラフとして照会できます。 

## <a name="getting-started"></a>使用の開始
Azure Cosmos DB アカウントは、Graph API をサポートする Azure CLI、Azure PowerShell、または Azure Portal を使用して作成できます。 アカウントを作成すると、Gremlin の WebSocket フロントエンドを提供する `https://<youraccount>.graphs.azure.com` などのサービス エンドポイントが Azure Portal で提供されます。 [Gremin コンソール](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)などの Gremlin 互換ツールを構成してこのエンドポイントに接続し、Java、Node.js、または任意の Gremlin クライアント ドライバーでアプリケーションを構築できます。

次の表に、Azure Cosmos DB に対して使用できる一般的な Gremlin ドライバーを示します。

| ダウンロード | ドキュメント |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.JS](https://www.npmjs.com/package/gremlin) |[Github の Gremlin-JavaScript](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin コンソール](https://tinkerpop.apache.org/downloads.html) |[TinkerPop ドキュメント](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB では、NuGet を使用して、[Azure Cosmos DB SDK](../documentdb/documentdb-sdk-dotnet.md) 上で Gremlin 拡張メソッドにファースト パーティの .NET ライブラリも提供します。 このライブラリは、DocumentDB データ パーティションに直接接続する際に使用できる "インプロセス" Gremlin サーバーを提供します。 

| ダウンロード | ドキュメント |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

[Azure Cosmos DB Emulator](../documentdb/documentdb-nosql-local-emulator.md) を使用すると、Azure サブスクリプションを作成したり、コストをかけたりせずに、Graph API を使用してローカルで開発とテストを行うことができます。 エミュレーターでのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Azure Cosmos DB のグラフ サポートのシナリオ
Azure Cosmos DB のグラフ サポートを使用できるシナリオを以下に示します。 

* **ソーシャル ネットワーク**: 顧客に関するデータと顧客による他のユーザーとのやり取りに関するデータを組み合わせることで、パーソナライズされたエクスペリエンスを開発したり、顧客の行動を予測したりできます。また、ユーザーを同じような興味を持つ他のユーザーと結び付けることもできます。 Azure Cosmos DB を使用して、ソーシャル ネットワークを管理し、顧客の嗜好やデータを追跡できます。 

* **レコメンデーション エンジン**: 小売業界で一般的に使用されます。 製品、ユーザー、ユーザー インタラクション (商品の購入、閲覧、評価など) に関する情報を組み合わせることで、カスタマイズされたレコメンデーションを構築できます。 低待機時間、エラスティック スケール、グラフのネイティブ サポートを実現する Azure Cosmos DB は、これらのインタラクションのモデル化に最適です。

* **地理空間**: 通信、物流、旅行プランニングの各分野の多くのアプリケーションでは、特定のエリア内で対象となる場所を見つけたり、2 つの場所間の最短ルートや最適ルートを見つけたりする必要があります。 Azure Cosmos DB はこれらの問題に自然に適合します。 

* **モノのインターネット**: ネットワークと、グラフとしてモデル化された IoT デバイス間の接続により、デバイスと資産の状態や、ネットワークのある部分の変更が別の部分に及ぼす可能性のある影響について理解を深めることができます。 

## <a name="next-steps"></a>次のステップ
Azure Cosmos DB のグラフ サポートの詳細については、以下をご覧ください。

* [Azure Cosmos DB グラフ チュートリアル](create-graph-dotnet.md)を開始する
* [Gremlin を使用して Azure Cosmos DB のグラフを照会する](gremlin-support.md)方法を確認する

