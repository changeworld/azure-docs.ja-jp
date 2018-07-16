---
title: Azure Cosmos DB Graph API の概要 | Microsoft Docs
description: Azure Cosmos DB を使用し、Apache TinkerPop の Gremlin グラフ クエリ言語を使って待ち時間の短い大規模なグラフの格納、クエリの実行、トラバースを行う方法について説明します。
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: 333bb4074ac741e854ff56c7c397b0e3be247f1b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857152"
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Azure Cosmos DB の概要: Graph API

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散マルチモデル データベース サービスです。 Azure Cosmos DB は、次の機能を提供します。これらの機能はすべて、[業界最先端の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。

* [ターンキー グローバル配信](distribute-data-globally.md)
* 世界規模での[スループットとストレージのエラスティック スケーリング](partition-data.md)
* 99 パーセンタイルで 10 ミリ秒未満の待ち時間
* [明確に定義された 5 種類の整合性レベル](consistency-levels.md)
* 高可用性の保証 

Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。

Kirill Gavrylyuk が Azure Cosmos DB でグラフを利用する方法について解説している次のビデオを視聴することをお勧めします。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Azure Cosmos DB Graph API には次のものが含まれています。

- グラフのモデリング。
- トラバーサル API。
- ターンキー グローバル配信。
- 10 ミリ秒未満の読み取り待ち時間と 99 パーセンタイルで 15 ミリ秒未満を実現したストレージとスループットのエラスティック スケーリング。
- インスタント クエリの可用性による自動インデックス設定。
- 調整可能な整合性レベル。
- 包括的な SLA。すべての単一リージョン アカウントと厳密でない一貫性のすべての複数リージョン アカウントで 99.99% の可用性 SLA、すべての複数リージョン データベース アカウントで 99.999% の読み取り可用性。

Azure Cosmos DB にクエリを実行するには、[Apache TinkerPop](http://tinkerpop.apache.org) のグラフ トラバーサル言語である [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) を使用できます。

この記事では、Azure Cosmos DB Graph API の概要と、これを使用して何十億もの頂点と辺のある大規模なグラフを保存する方法について説明します。 ミリ秒の待機時間で、グラフにクエリを実行し、グラフの構造とスキーマを簡単に改善できます。

## <a name="graph-database"></a>グラフ データベース
実世界で出現するデータは、自然に結び付けられています。 従来のデータ モデリングはエンティティに重点を置いています。 しかし、多くのアプリケーションでは、当然、エンティティとリレーションシップの両方をモデル化するというニーズがあります。

[グラフ](http://mathworld.wolfram.com/Graph.html)とは、[頂点](http://mathworld.wolfram.com/GraphVertex.html)と[辺](http://mathworld.wolfram.com/GraphEdge.html)で構成された構造です。 頂点と辺は、いずれも任意の数のプロパティを持つことができます。 頂点は、ユーザー、場所、イベントなどの個々のオブジェクトを表します。 辺は、頂点間のリレーションシップを表します。 たとえば、あるユーザーは、別のユーザーと知り合いで、あるイベントに関連があり、ある場所に最近行ったような場合です。 プロパティは頂点と辺に関する情報を表します。 プロパティの例には、名前や年齢、辺を持つ頂点が含まれ、タイムスタンプや重みなどを持ちます。 正式には、このモデルは[プロパティ グラフ](http://tinkerpop.apache.org/docs/current/reference/#intro)と呼ばれます。 Azure Cosmos DB は、プロパティ グラフ モデルをサポートしています。

たとえば、次のサンプル グラフは、ユーザー、モバイル デバイス、関心事、オペレーティング システムの間のリレーションシップを示しています。

![ユーザー、デバイス、関心事を示すサンプル データベース](./media/graph-introduction/sample-graph.png)

グラフは、科学、テクノロジ、ビジネスにおけるさまざまなデータセットを理解するうえで役立ちます。 グラフ データベースにより、グラフを効率的かつ自然にモデル化して格納できるので、さまざまなシナリオでグラフが有益なものになります。 これらのユース ケースでは、スキーマの柔軟性と迅速な反復処理も必要であることが多いため、通常、グラフ データベースは NoSQL データベースです。

グラフは斬新で強力なデータ モデリング手法です。 ただし、この事実だけでは、グラフ データベースを使用する十分な理由にはなりません。 グラフ トラバーサルを必要とする多くのユース ケースやパターンでは、グラフは従来の SQL データベースや NoSQL データベースよりもパフォーマンスがはるかに優れています。 このパフォーマンスの違いは、友人の友人のような複数のリレーションシップをトラバースするときにさらに増幅されます。

グラフ データベースが提供する高速トラバーサルと、深さ優先探索、幅優先探索、ダイクストラ法などのグラフ アルゴリズムを組み合わせることで、ソーシャル ネットワーク、コンテンツ管理、レコメンデーションなどのさまざまな分野の問題を解決できます。

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Azure Cosmos DB による地球規模のグラフ
Azure Cosmos DB は、グローバル配布、ストレージとスループットのエラスティック スケーリング、自動インデックス作成とクエリ、調整可能な整合性レベルを提供し、TinkerPop 標準をサポートする、完全に管理されたグラフ データベースです。

![Azure Cosmos DB グラフ アーキテクチャ](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB は、市場の他のグラフ データベースと比較した場合、差別化する次の機能を備えています。

* 弾力的にスケーラブルなスループットとストレージ

 実際のグラフは、1 つのサーバーの容量を超えてスケールする必要があります。 Azure Cosmos DB では、グラフを複数のサーバー間でシームレスにスケールできます。 また、アクセス パターンに基づいて、独立してグラフのスループットをスケールすることもできます。 Azure Cosmos DB は、実質的に無制限のストレージ サイズとプロビジョニング スループットにスケールできるグラフ データベースをサポートしています。

* 複数リージョンのレプリケーション

 Azure Cosmos DB では、自分のアカウントに関連付けておいたすべてのリージョンにグラフ データが透過的にレプリケートされます。 レプリケーションを使用すると、データへのグローバル アクセスを必要とするアプリケーションを開発できます。 ただし、整合性、可用性、パフォーマンスと対応する保証の面で妥協が必要になります。 Azure Cosmos DB では、マルチホーム API を使用した透過的なリージョン内フェールオーバーを提供しています。 スループットとストレージを世界規模で弾力的にスケールすることもできます。

* 使い慣れた Gremlin 構文を使用した高速のクエリとトラバーサル

 異種の頂点と辺を格納し、使い慣れた Gremlin 構文を使用してこれらのドキュメントを照会できます。 Azure Cosmos DB では、同時実行性の高い、ロックのないログ構造のインデックス作成技術を利用して、すべてのコンテンツのインデックスを自動的に作成します。 そのため、スキーマのヒント、セカンダリ インデックス、またはビューを指定しなくても、豊富なリアルタイム クエリとトラバーサルが可能になります。 詳細については、[Gremlin を使用したグラフの照会](gremlin-support.md)に関する記事を参照してください。

* 完全管理型

 Azure Cosmos DB を使用すると、データベースやコンピューター リソースを管理する手間がかかりません。 Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、スケールの管理、複雑なデータ層のアップグレードを手作業で行う必要はありません。 すべてのグラフが自動的にバックアップされ、リージョンの障害から保護されます。 Azure Cosmos DB アカウントを簡単に追加し、必要に応じて容量をプロビジョニングできるので、データベースの運用と管理ではなく、アプリケーションに注力できます。

* インデックスの自動作成

 既定では、Azure Cosmos DB はグラフのノードおよび辺内のすべてのプロパティのインデックスを自動的に作成するため、スキーマや、セカンダリ インデックスの作成は不要です。

* Apache TinkerPop との互換性

 Azure Cosmos DB は、オープン ソースの Apache TinkerPop 標準をネイティブでサポートしており、他の TinkerPop 対応グラフ システムと統合できます。 そのため、Titan や Neo4j などの別のグラフ データベースから簡単に移行することができます。また、Apache Spark GraphX などのグラフ分析フレームワークで Azure Cosmos DB を使用することもできます。

* 調整可能な整合性レベル

 整合性とパフォーマンスの最適なトレードオフを実現するために、明確に定義された 5 つの整合性レベルの中から選択できます。 Azure Cosmos DB では、クエリと読み取り操作に関して、強固、有界整合性制約、セッション、一貫性のあるプレフィックス、最終的の 5 種類の整合性レベルを提供します。 明確に定義されたきめ細かな整合性レベルにより、整合性、可用性、待機時間の適切なトレードオフを行うことができます。 詳しくは、「[Azure Cosmos DB の調整可能なデータの一貫性レベル](consistency-levels.md)」をご覧ください。

Azure Cosmos DB では、同じコンテナーやデータベース内でドキュメントやグラフなどの複数のモデルを使用することもできます。 ドキュメント コンテナーを使用してグラフ データをドキュメントと共に格納できます。 JSON に対する SQL クエリと Gremlin クエリの両方を使用して、同じデータをグラフとして照会できます。

## <a name="get-started"></a>作業開始
Graph API をサポートする Azure コマンド ライン インターフェイス (CLI)、Azure PowerShell、または Azure Portal を使用して、Azure Cosmos DB アカウントを作成できます。 アカウントを作成すると、Gremlin の WebSocket フロントエンドを提供する `https://<youraccount>.gremlin.cosmosdb.azure.com` などのサービス エンドポイントが Azure Portal で提供されます。 [Gremin コンソール](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)などの TinkerPop 互換ツールを構成してこのエンドポイントに接続し、Java、Node.js、または任意の Gremlin クライアント ドライバーでアプリケーションを構築できます。

次の表に、Azure Cosmos DB に対して使用できる一般的な Gremlin ドライバーを示します。

| [ダウンロード] | ドキュメント | Getting Started (概要) |
| --- | --- | --- |
| [.NET](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Github の Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [.NET を使用してグラフを作成する](create-graph-dotnet.md) |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) | [Java を使用してグラフを作成する](create-graph-java.md) |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub の Gremlin-JavaScript](https://github.com/jbmusso/gremlin-javascript) | [Node.js を使用してグラフを作成する](create-graph-nodejs.md) |
| [Python](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [GitHub の Gremlin-Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Python を使用してグラフを作成する](create-graph-python.md) |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Github の Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [PHP を使用してグラフを作成する](create-graph-php.md) |
| [Gremlin コンソール](https://tinkerpop.apache.org/downloads.html) | [TinkerPop ドキュメント](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gremlin コンソールを使用してグラフを作成する](create-graph-gremlin-console.md) |

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Azure Cosmos DB のグラフ サポートのシナリオ
Azure Cosmos DB のグラフ サポートを使用できるシナリオを以下に示します。

* ソーシャル ネットワーク

 顧客に関するデータと顧客による他のユーザーとのやり取りに関するデータを組み合わせることで、パーソナライズされたエクスペリエンスを開発したり、顧客の行動を予測したりできます。また、ユーザーを同じような興味を持つ他のユーザーと結び付けることもできます。 Azure Cosmos DB を使用して、ソーシャル ネットワークを管理し、顧客の嗜好やデータを追跡できます。

* 推奨エンジン

 このシナリオは、小売業界で一般的に使用されます。 製品、ユーザー、ユーザー インタラクション (商品の購入、閲覧、評価など) に関する情報を組み合わせることで、カスタマイズされたレコメンデーションを構築できます。 低待機時間、エラスティック スケール、グラフのネイティブ サポートを実現する Azure Cosmos DB は、これらのインタラクションのモデル化に最適です。

* GeoSpatial

 通信、物流、旅行プランニングの各分野の多くのアプリケーションでは、エリア内で対象となる場所を見つけたり、2 つの場所間の最短ルートや最適ルートを見つけたりする必要があります。 Azure Cosmos DB はこれらの問題に自然に適合します。

* モノのインターネット (IoT)

 ネットワークと、グラフとしてモデル化された IoT デバイス間の接続により、デバイスと資産の状態について理解を深めることができます。 さらに、ネットワークのある部分の変更が別の部分に及ぼす可能性のある影響についても学習できます。

## <a name="next-steps"></a>次の手順
Azure Cosmos DB のグラフ サポートの詳細については、以下をご覧ください。

* [Azure Cosmos DB グラフ チュートリアル](create-graph-dotnet.md)を開始する
* [Gremlin を使用して Azure Cosmos DB のグラフを照会する](gremlin-support.md)方法を確認する。
