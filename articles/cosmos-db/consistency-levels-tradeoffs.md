---
title: Azure Cosmos DB のさまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ
description: Azure Cosmos DB のさまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 52f08511d16e216ced7e3d1de11eae960cdbaeb8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041868"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB のさまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ

高可用性、短い待機時間、またはその両方のためにレプリケーションに依存している分散型データベースでは、トレードオフを考慮する必要があります。 読み取りの整合性と、可用性、待機時間、およびスループットとのトレードオフです。 

Azure Cosmos DB では、幅広い選択肢を利用できるデータ整合性に対応しています。 このアプローチには、厳密で最終的な整合性という 2 つの極端な方法よりも多くの選択肢があります。 整合性の程度に応じて明確に定義された 5 種類のモデルから選択することができます。 最強から最弱の順に、次のモデルがあります。

- Strong 
- Bounded staleness 
- Session 
- 一貫性のあるプレフィックス 
- Eventual 

いずれのモデルにも可用性とパフォーマンスのトレードオフがあり、包括的な SLA によってサポートされています。

## <a name="consistency-levels-and-latency"></a>整合性レベルと待機時間

- すべての整合性レベルの読み取り待機時間は 99 パーセンタイルで 10 ミリ秒未満となるように常に保証されています。 この読み取り待機時間は、SLA によってサポートされます。 平均読み取り待機時間は、(50 パーセンタイルで) 通常 2 ミリ秒以下です。 複数のリージョンにまたがり、厳密な整合性で構成されている Azure Cosmos アカウントは、この保証の例外です。

-  その他の整合性レベルの書き込み待機時間は 99 パーセンタイルで 10 ミリ秒未満となるように常に保証されています。 この書き込み待機時間は、SLA によってサポートされます。 平均書き込み待機時間は、(50 パーセンタイルで) 通常 5 ミリ秒以下です。

Azure Cosmos アカウントによっては、複数のリージョンが厳密な整合性で構成されている可能性があります。 このような場合、書き込み待機時間は、99 パーセンタイルでラウンド トリップ時間 (RTT) の 2 倍 + 10 ミリ秒未満になるように保証されています。 最も遠く離れた任意の 2 つのリージョン間の RTT が Azure Cosmos アカウントに関連付けられます。 これは、Azure Cosmos アカウントに関連付けられている最も遠く離れた任意の 2 つのリージョン間の RTT と同じです。 このオプションは現在プレビューの段階です。 

正確な RTT 待機時間は、光の速さで通信する距離と Azure ネットワーク トポロジによって決まります。 Azure ネットワークでは、任意の 2 つの Azure リージョン間の RTT に対して待機時間の SLA は提供されません。 Azure Cosmos アカウントの場合、レプリケーションの待機時間は Azure portal に表示されます。 Azure portal を使用すると、自分のアカウントに関連付けられている多様なリージョン間のレプリケーションの待機時間を監視できます。

## <a name="consistency-levels-and-throughput"></a>整合性レベルとスループット

- 要求ユニットの数が同じである場合、"セッション"、"一貫性のあるプレフィックス"、"最終的" の整合性レベルでは、"厳密" および "有界整合性制約" の場合と比べて約 2 倍の読み取りスループットを発揮します。

- 書き込み操作の種類 (挿入、置換、アップサート、削除) が指定された場合、要求ユニットに対するスループットはすべての整合性レベルで同じです。

## <a name="next-steps"></a>次の手順

分散システムでのグローバル分散および一般的な整合性のトレードオフについて学習します。 次の記事を参照してください。

* [最新の分散データベース システム設計における整合性のトレードオフ](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [高可用性](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
