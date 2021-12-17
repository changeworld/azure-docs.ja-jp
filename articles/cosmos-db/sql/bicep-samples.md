---
title: Azure Cosmos DB Core の Bicep サンプル (SQL API)
description: Bicep を使用して Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699723"
---
# <a name="bicep-for-azure-cosmos-db"></a>Azure Cosmos DB の Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では Core (SQL) API アカウントの Bicep サンプルを示します。 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md)、[Table](../table/manage-with-bicep.md) の API の Bicep サンプルもあります。

## <a name="core-sql-api"></a>コア (SQL) API

|**サンプル**|**説明**|
|---|---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-bicep.md#create-autoscale) | データベースとコンテナーの 2 つのリージョンに、自動スケーリングのスループットでコア (SQL) API アカウントを作成します。 |
|[分析ストアで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-bicep.md#create-analytical-store) | 分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-bicep.md#create-manual) | データベースとコンテナーの 2 つのリージョンに、標準スループットでコア (SQL) API アカウントを作成します。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-with-bicep.md#create-sproc) | コンテナーのストアド プロシージャ、トリガー、UDF を使用し、2 つのリージョンにコア (SQL) API アカウントを作成します。 |
|[Azure AD ID、ロールの定義、およびロールの割り当てを使用して Azure Cosmos アカウントを作成する](manage-with-bicep.md#create-rbac) | サービス プリンシパルで Azure AD ID、ロールの定義、ロールの割り当てを使用してコア (SQL) API アカウントを作成します。 |
|[Free レベルの Azure Cosmos アカウントを作成する](manage-with-bicep.md#free-tier) |  free レベルで Azure Cosmos DB Core (SQL) API アカウントを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。

* 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください
