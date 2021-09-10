---
title: Azure Cosmos DB Core (SQL API) の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 58c416ec16cbfdade541119b88fe0524071e4b3b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114163"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、コア (SQL) API アカウント用の Azure Resource Manager テンプレートの例のみを示します。 [Cassandra](../cassandra/templates-samples.md)、[Gremlin](../graph/resource-manager-template-samples.md)、[MongoDB](../mongodb/resource-manager-template-samples.md)、[Table](../table/resource-manager-templates.md) の API の例もあります。

## <a name="core-sql-api"></a>コア (SQL) API

|**テンプレート**|**説明**|
|---|---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-autoscale) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、自動スケーリングのスループットでコア (SQL) API アカウントを作成します。 |
|[分析ストアで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-analytical-store) | このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-manual) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、標準のスループットでコア (SQL) API アカウントを作成します。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-with-templates.md#create-sproc) | このテンプレートは、コンテナーのストアド プロシージャ、トリガー、および UDF を使用して、2 つのリージョンに コア (SQL) API アカウントを作成します。 |
|[Azure AD ID、ロールの定義、およびロールの割り当てを使用して Azure Cosmos アカウントを作成する](manage-with-templates.md#create-rbac) | このテンプレートは、サービス プリンシパルで Azure AD ID、ロールの定義、およびロールの割り当てを使用してコア (SQL) API アカウントを作成します。 |
|[既存の Azure Cosmos アカウントに対するプライベート エンドポイントを作成する](../how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos Core (SQL) API アカウントのプライベート エンドポイントを作成します。 |
|[Free レベルの Azure Cosmos アカウントを作成する](manage-with-templates.md#free-tier) |  このテンプレートは、free レベルで Azure Cosmos DB Core (SQL) API アカウントを作成します。 |

リファレンス ドキュメントについては、[Azure Cosmos DB 用 Azure Resource Manager リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB への移行のための容量計画を立てる場合。 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください