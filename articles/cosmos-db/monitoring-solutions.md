---
title: サード パーティ製の監視ツールを使用して Azure Cosmos DB を監視する
description: この記事では、Cosmos DB の監視に役立つサードパーティ製のツールについて説明します。
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778885"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>サード パーティ ソリューションを使用して Azure Cosmos DB を監視する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor とは別に、サード パーティ製の監視ソリューションを使用して、Cosmos DB インスタンスを監視できます。

> [!IMPORTANT] 
> この記事で説明されているソリューションは情報提供のみを目的としており、所有権は個々のソリューションの所有者にあります。 十分な評価を行ってから、最適なものを選択することをお勧めします。

## <a name="datadog"></a>Datadog
{サポート対象: SQL API、MongoDB 用 Azure Cosmos DB API、Gremlin API、 Cassandra API および Table API}

[Datadog](https://www.datadoghq.com/) は、インフラストラクチャの監視、アプリケーション パフォーマンスの監視、ログ管理、ユーザー エクスペリエンスの監視などを網羅する完全に統合されたプラットフォームです。 会社のスタック内のすべてのツールとサービスのデータを結集することで、Datadog はトラブルシューティング、パフォーマンスの最適化、チーム間コラボレーションのための信頼できる唯一の情報源を提供します。
Datadog 内のすべての内容が同じタグ セットの下に編成されるため、特定の問題に関連するすべてのデータが自動的に関連付けられます。 Datadog を使用すると、盲点を排除することで、見落とされたエラーのリスクが軽減され、継続的なサービス メンテナンスの負担が軽減され、デジタル トランスフォーメーションが加速されます。

Datadog によって、CosmosDB から、リージョンあたりの使用可能なストレージの合計、作成された SQL データベースの数など、40 を超えるさまざまなゲージおよびカウント メトリックが収集されます。 これらのメトリックは Datadog と Azure の統合を通じて収集され、業界他社より 40% 速くプラットフォームに表示されます。 Datadog には、CosmosDB 用のすぐに使用できるダッシュボードも用意されていて、CosmosDB インスタンスのパフォーマンスに関する分析情報が即時に表示されます。 ユーザーは、プラットフォーム レベルのメトリック (使用された要求ユニットの合計など) と API レベルのメトリック (作成された Cassandra キースペースの数など) を視覚化して、Cosmos DB の使用状況をより深く理解できます。

Datadog は、以下のような、Cosmos DB のさまざまなお客様に使用されています
- Maersk
- PWC 
- PayScale 
- AllScripts 
- Hearst



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Datadog のデモ" border="false":::
**図:** 実行中の Datadog

役に立つリンク:
- [価格の詳細](https://www.datadoghq.com/pricing/)
- [14 日間の試用版を使い始める](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{サポート対象: MongoDB 用 SQL API および Azure Cosmos DB API}

[Dynatrace](https://www.dynatrace.com/platform/) を使用すると、クラウド向けのソフトウェア インテリジェンスが提供されることで、クラウドの複雑性が緩和され、デジタル フォーメーションが加速されます。 自動的かつインテリジェントな監視を大規模に行うことで、Dynatrace のオールインワンのソフトウェア インテリジェンス プラットフォームは、アプリケーションのパフォーマンスとセキュリティ、基になるインフラストラクチャ、すべてのユーザーのエクスペリエンスに関する正解をもたらします。それにより、チームはクラウド運用を自動化し、向上したソフトウェアをより迅速にリリースし、比類のないデジタル エクスペリエンスを提供できます。  
Dynatrace では Mongo API を使用して Cosmos DB メトリックが収集および配信されます。これには、呼び出しの数と応答時間が含まれ、集計、コマンド、読み取り/書き込み操作に従ってすべてが視覚化されます。  また、環境内で実行されているデータベース ステートメントも正確に示されます。  最後に、[Davis AI Engine](https://www.dynatrace.com/davis) の機能を利用することで、どのデータベース ステートメントが機能低下の根本原因かを正確に検出でき、根本原因として特定されたデータベースを確認できます。

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Cosmos DB の監視情報を提供する Dynatrace のさまざまな画面" border="false":::
**図:** 実行中の Dynatrace

### <a name="useful-links"></a>便利なリンク

- [15 日間の無料試用版で Dynatrace を試す](https://www.dynatrace.com/trial)
- [Azure Marketplace から起動する](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [Azure Monitor で Cosmos DB を使用する方法に関するドキュメント](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB - Dynatrace の統合の詳細](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [Azure データベースの Dynatrace による監視](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Azure 向け Dynatrace ソリューションの概要](https://www.dynatrace.com/technologies/azure-monitoring/)
- [ソリューション パートナー](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>次のステップ
- [Cosmos DB データの監視のリファレンス](./monitor-cosmos-db-reference.md)
