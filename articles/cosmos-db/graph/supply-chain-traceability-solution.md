---
title: Azure Cosmos DB Gremllin API を使用した Infosys サプライ チェーン トレーサビリティ ソリューション
description: Infosys によって実装されているサプライ チェーンのトレーサビリティ グラフ ソリューションでは、Azure Cosmos DB Gremlin API とその他の Azure サービスを使用しています。 このソリューションでは、グローバル サプライ チェーンの追跡と完成した商品に対するトレース機能が提供されます。
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 3dacf188e0a3fbf901a97ff2125788f080d6532e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003896"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API を使用したサプライ チェーン トレーサビリティ ソリューション

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

この記事では、[Infosys によって実装されるトレーサビリティ グラフ ソリューション](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)の概要を説明します。 これらのソリューションでは、グローバル サプライ チェーンの追跡と完成した商品に対するトレース機能を提供するために、Azure Cosmos DB Gremlin API とその他の Azure 機能が使用されています。

この記事を読むと、次のことを学習できます。

* サプライ チェーンのコンテキストにおける追跡可能性とは何か。
* Azure 機能を使用して提供される、グローバルなトレーサビリティ ソリューションのアーキテクチャ。  
* Azure Cosmos DB グラフ データベースが、グローバル サプライ チェーンにおいて原材料と完成品の間の複雑なリレーションシップをどのように支援しているか。
* Event Hubs、API Management などの Azure 統合プラットフォーム サービスが、さまざまなサプライ チェーン アプリケーションのエコシステムを統合するのにどのように役立っているか。
* 追跡可能性に関するニーズを満たすために、Infosys のこのソリューションを利用することで、どのようなメリットが得られるか。

## <a name="overview"></a>概要

食品サプライ チェーンでは、商品の追跡可能性として、商品のライフサイクル全体にわたり、サプライ チェーン全体で "追跡およびトレース" できることが求められます。 そのサプライ チェーンには、供給、製造、配送が含まれます。 追跡可能性は、さまざまな規制にさらされる中で、食べ物の安全性やブランドを守っていくのに必要不可欠です。 これまで、一部の組織では、サプライ チェーンで商品を効果的に追跡およびトレースできなかったため、コストのかかるリコールを行ったり、罰金を支払ったり、消費者の健康問題を引き起こしたりしてきました。 トレーサビリティ ソリューションでは、さまざまな速度と正確性でデータの調和やデータ インジェストのニーズに対処する必要があります。さらに重要なのは、従来のプラットフォームでは実現できないインベントリ サイクルの目標に従う必要があることです。

アプリケーション サービス、統合サービス、データベース サービスなどの Azure 機能を使用して開発された Infosys のトレーサビリティ ソリューションは、次のような重要な機能を提供します。

* 工場、倉庫、流通センターを接続します。
* 並行在庫移動イベントを取り込み、処理します。
* 原材料、バッチ、完成品 (FG) パレット、複数レベルの親子関係のパレット、商品の移動の間のつながりを示すナレッジ グラフ。
* ワイルドカード検索から特定キーワード検索までの範囲の検索機能を備えたユーザー ポータル。
* 影響を受ける原材料バッチ、影響を受けるパレット、それらのパレットの場所など、品質インシデントの影響を特定します。
* 製品のリコール情報など、複数の市場でイベントの履歴を取得する機能。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

サプライ チェーンの追跡可能性では、通常、パレット移動の取り込み、品質インシデントの処理、店舗データのトレース/分析におけるパターンが共有されます。 最初に、これらのシステムでは、地理的に離れた工場および倉庫管理システムから大量のデータを取り込む必要があります。 次に、これらのシステムでは、ストリーミング データを処理および分析して、原材料、生産バッチ、完成品パレット、複雑な親子関係 (共同パック/再パック) 間にみられる複雑なリレーションシップを導き出します。 その後、システムで、原材料、完成した商品、パレットの間の複雑なリレーションシップに関する情報、トレーサビリティのために必要なすべてのものを保存する必要があります。 検索機能を備えたユーザー ポータルでは、サプライ チェーン ネットワーク内の商品を追跡およびトレースすることができます。 これらのサービスにより、クラウドネイティブ、API ファースト、データ駆動型の機能をサポートするエンドツーエンドのトレーサビリティ ソリューションが実現します。

Microsoft Azure には、Azure Cosmos DB、Azure Event Hubs、Azure API Management、Azure App Service、Azure SignalR、Azure Synapse Analytics、Power BI など、追跡可能性に関するユース ケースに利用できる豊富なサービスが用意されています。

Infosys のトレーサビリティ ソリューションは、追跡とトレースの機能を向上させるために使用できる、事前に準備されたソリューションを提供します。 次の図は、このトレーサビリティ ソリューションで使用されるアーキテクチャについて説明しています。

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="サプライ チェーン トレーサビリティ ソリューションのアーキテクチャ" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

このアーキテクチャで使用されるさまざまな Azure サービスは、次のタスクに役立ちます。

* Azure Cosmos DB では、パフォーマンスを柔軟にスケール アップまたはスケール ダウンできます。 Gremlin API では、原材料、完成品、倉庫間の複雑なリレーションシップを構築し、クエリを実行することができます。
* Azure API Management は、3PL (サード パーティ ロジスティック プロバイダー) と倉庫管理システム (WMS) に対する在庫移動イベントのための API を提供します。  
* Azure Event Hubs では、さらに処理を行うために WMS と 3PL から多数の同時イベントを収集することができます。
* Azure Function アプリは、Gremlin API を使用して、イベントを処理し、Azure Cosmos DB にデータを取り込みます。
* Azure Search サービスを使用すると、ユーザーは複雑な検索、パレット情報のフィルター処理を行うことができます。
* Azure Databricks では、変更フィードを読み取り、Synapse Analytics でモデルを作成して、Power BI を利用しているユーザーのためにセルフサービス レポートを作成します。
* Azure Web アプリと App Service プランを使用すると、ユーザー ポータルをデプロイできます。
* Azure Storage アカウントには、長期間データを保管することが義務付けられる規制に対応するため、アーカイブ済みのデータが格納されます。

## <a name="graph-db-and-its-data-design"></a>Graph DB とそのデータ設計

商品の生産と配送では、複雑で動的な一連のリレーションシップを維持する必要があります。  当社のトレーサビリティ グラフの適応型データ モデルを使用すると、原材料の受領から始まり、工場で完成品を製造し、サプライ チェーンのさまざまな倉庫に転送し、最終的に顧客の倉庫に転送するといったリレーションシップを保存できます。 プロセスの全体像を視覚化すると、次の図のようになります。

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="サプライ チェーン データ デザインの視覚化" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

上の図には、複雑なサプライ チェーン プロセスの概要と簡略化されたビューが示されています。 ただし、これらの多種多様な情報すべてを接続する複雑なグラフを作成するには、工場および倉庫からの重要な在庫移動情報をリアル タイムで取得する必要があります。

1. トレーサビリティ プロセスはサプライヤーが原材料を工場に送信すると開始され、グラフとリレーションシップ (エッジ) の初期ノード (頂点) が作成されます。

1. 原材料から完成品 (アイテム) が製造され、パレットに積み込まれます。

1. 次に、顧客の需要/注文ごとに、パレットが工場出荷時の倉庫または市場の倉庫に移動されます。

1. 倉庫は、会社所有のものである場合も、3PL (サード パーティ ロジスティック プロバイダー) のものである場合もあります。 その後、顧客の注文に応じて、他のさまざまな倉庫にパレットが発送されます。 顧客の需要に応じて、注文数量に合わせて子パレットまたは孫パレットが作成されます。 場合によっては、複数のアイテムを混在させることによって、まったく新しいアイテムが作成されます。 たとえば、さまざまなパックを生成するパッキング シナリオでは、顧客注文の一部として、より少ない、またはより多い数量に変更するため、同じアイテムを異なるパレットにパックし直すことがあります。

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="サプライ チェーン トレーサビリティ ソリューションにおけるパレット リレーションシップ" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. その後、サプライ チェーン ネットワークを介して、最終的に顧客の倉庫に到達します。 その過程で、パレットをさらに分割したり、他のパレットと結合して、顧客の注文を満たす新しいパレットを作成したりすることができます。

1. 最終的には、品質インシデント管理の重要なリレーションシップ情報を保持する複雑なグラフが作成されます。これについては、後で説明します。

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="サプライ チェーン オブジェクト リレーションシップの完全なアーキテクチャ" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. これらの複雑なリレーションシップは、システムがサプライ チェーン全体のパレットを追跡およびトレースできる品質インシデントに必要不可欠です。 グラフとそのトラバーサルによって、このに必要な情報が提供されます。 たとえば、ある原材料に問題がある場合、グラフには影響を受けたパレット、現在の場所が表示されます。

## <a name="next-steps"></a>次の手順

* [Infosys トレーサビリティ グラフ ソリューション](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* [Infosys Integrate+ for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-integrate-for-azure)
* グラフ データを視覚化するには、[Gremlin API 可視化ソリューション](graph-visualization-partners.md)に関する記事を参照してください。
* グラフ データをモデル化するには、[Gremlin API モデリング ソリューション](graph-modeling-tools.md)に関する記事を参照してください。
