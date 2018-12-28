---
title: Azure Cosmos DB のリージョン プレゼンス
description: この記事では、Azure Cosmos DB のリージョン プレゼンスとさまざまなクラウド環境について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: c3299b2ea777577281ede234cd1aa5137ce5a445
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138111"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Azure Cosmos DB のリージョン プレゼンス

現時点で、世界中の [54 のリージョン](https://azure.microsoft.com/global-infrastructure/regions/)で Azure を利用できます。 Azure Cosmos DB は Azure の 基礎を成すサービスであり、Azure を利用できるすべてのリージョンで利用できます。

![Azure Cosmos DB の各リージョンでの可用性](./media/regional-presence/regional-presence.png)

Cosmos DB は、お客様が利用できる 5 つの異なる Azure クラウド環境のすべてで利用できます。

* **Azure パブリック** クラウドは、グローバルに利用できます。

* **Azure China** は、マイクロソフトと中国最大のインターネット プロバイダーの 1 つである 21Vianet との間の独自のパートナーシップを通して利用できます。

* **Azure Germany** では、データ保護受託者モデルに沿ってサービスが提供され、お客様のデータは、ドイツ国内でデータ保護受託者の役目を務める T-Systems International GmbH (Deutsche Telekom の子会社) の管理下で、ドイツ国外に移転されないことが保証されます。

* **Azure Government** は、米国内の 4 つのリージョンで米国政府機関とそのパートナーが利用できます。 

* **米国国防総省 (DoD) 向け Azure Government** は、米国内の 2 つのリージョンで米国国防総省が利用できます。

## <a name="regional-presence-with-global-distribution"></a>グローバル分散によるリージョン プレゼンス

すべての Azure リージョンで、Azure Cosmos DB によって公開されるさまざまな API (SQL、MongoDB、Cassandra、Gremlin、Azure Table Storage など) を利用できます。 たとえば、Azure Cosmos DB によって公開される MongoDB API と Cassandra API を、世界中のすべての Azure リージョンだけではなく、Azure 独立リージョン (China、Germany、Government、米国国防総省 (DoD)) でも利用できます。

Azure Cosmos DB は、[グローバルに分散される](distribute-data-globally.md)データベースです。 任意の数の Azure リージョンをお客様の Azure Cosmos アカウントに関連付けることができ、お客様のデータは、自動的かつ透過的にレプリケートされます。 Azure Cosmos アカウントへのリージョンの追加と削除は、いつでも実行できます。 Azure Cosmos DB では、ターンキー グローバル分散機能とマルチマスター レプリケーション プロトコルによって、読み取りと書き込みが 10 ミリ秒未満の待機時間で 99 パーセンタイルで実行され、読み取りと書き込みに対して 99.999 % の可用性が提供され、お客様の Azure Cosmos アカウントに関連付けられたすべてのリージョン間でプロビジョニング スループットを柔軟にスケーリングすることができます。 Azure Cosmos DB では、5 つの明確に定義された整合性モデルも用意されており、お客様は特定の整合性モデルをお客様のデータに適用できます。 最後に、Azure Cosmos DB は、包括的なプロビジョニング スループット、99 パーセンタイルの待機時間、高可用性、および一貫性を含む包括的なサービス レベル アグリーメント (SLA) を提供する業界唯一のデータベース サービスです。

## <a name="next-steps"></a>次の手順

次の記事で Azure Cosmos DB のさまざまな概念を確認できます。

* [グローバルなデータの分散](distribute-data-globally.md)
* [Azure Cosmos DB アカウントの管理方法](manage-account.md)
* [Azure Cosmos のコンテナーとデータベースのスループットをプロビジョニングする](set-throughput.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
