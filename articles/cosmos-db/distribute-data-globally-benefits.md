---
title: Azure Cosmos DB のグローバルな分散の主な利点
description: Azure Cosmos DB のマルチマスター、Geo レプリケーション、マルチマスター、およびそれが役に立つユースケースについて説明します。
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968349"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB でデータをグローバルに分散させる

この記事では、データをグローバルに分散させることの主なメリットと、グローバルなデータの分散が必要ないくつかのリアルタイム シナリオについて説明します。

## <a name="key-benefits"></a>主な利点

アカウントで Azure Cosmos DB のグローバル データ分散機能を利用する主な利点は以下のとおりです。

* **1 桁の待機時間** – Azure Cosmos DB では 99 パーセンタイルで 10 ミリ秒未満の読み書き待機時間が提供され、[利用料金に基づく SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によって保証されています。

* **ファイブ ナインの可用性** – Azure Cosmos DB では 99.999% の読み書き可用性が提供され、[利用料金に基づく SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によって保証されています。

* **柔軟な競合解決** – マルチマスター機能を利用するお客様に対して、Azure Cosmos DB では、グローバル データの整合性と一貫性を確保するための競合の処理について、3 つのモードが提供されます。

* **調整可能な整合性** – Azure Cosmos DB では、マルチマスター機能を使用するアカウントに対する強力な一貫性を除くすべてをサポートすることを考慮して、グローバル分散で 5 種類の[整合性レベル](consistency-levels.md)がサポートされています。

* **暗黙的なフォールト トレランス** – 複数のリージョン間でレプリケートされるデータにより、アプリケーションにはリージョン内の障害に対して高いフォールト トレランスが提供されます。

## <a name="use-cases"></a>ユース ケース

Azure Cosmos DB のグローバル分散機能を利用できるシナリオのほんの小さなサンプルを次に示します。

* **ソーシャル メディア アプリ** – ソーシャル メディア アプリケーションでは、世界中にいるユーザーに優れたエクスペリエンスを提供するため、低待機時間、高可用性、スケーラビリティが必要です。

* **IoT** – 地理的に分散したエッジ デプロイでは、多くの場合、複数の場所から時系列データを追跡する必要があります。 各デバイスは、最も近いリージョンに所属できます。 デバイスが別の場所に移動したときは、使用可能な最も近いリージョンに書き込むように、デバイスの所属を変更できます。

* **eコマース** – eコマースでは、非常に優れた低待機時間と高可用性が必要です。 読み取りと書き込みに関するデータの地理的分散により、データはユーザーに最も近い場所に置かれ、アプリケーションの応答性が向上します。 複数リージョンでの読み取りと書き込み両方に対する可用性により、さらに高い可用性が提供されます。

* **不正行為/異常の検出** – 多くの場合、ユーザー アクティビティやアカウント アクティビティを監視するアプリケーションはグローバルに分散しており、スコアを更新してリスク メトリックを正常範囲に保つために複数のイベントを同時に追跡する必要があります。

* **計測** – 使用量 (API 呼び出し回数、1 秒あたりのトランザクション数、使用時間 (分) など) のカウントと調整は、Azure Cosmos DB のマルチマスターを使用することで簡単にグローバルに実装できます。 組み込みの競合解決により、カウントの精度と調整の両方がリアルタイムで保証されます。

## <a name="next-steps"></a>次の手順  

この記事では、Azure Cosmos DB のグローバル データ無難機能の主な利点とユース ケースについて説明しました。 次に、以下のリソースをご覧ください。

* [Azure Cosmos DB でターンキー グローバル分散を有効にする方法](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB のグローバル データベース レプリケーションを構成する方法](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB アカウントのマルチマスターを有効にする方法](enable-multi-master.md)

* [Azure Cosmos DB での自動および手動フェールオーバーの動作](regional-failover.md)

* [Azure Cosmos DB での競合の解決について](multi-master-conflict-resolution.md)

* [オープン ソース NoSQL データベースでのマルチマスターの使用](multi-master-oss-nosql.md)
