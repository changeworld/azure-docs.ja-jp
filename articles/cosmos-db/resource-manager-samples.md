---
title: Azure Cosmos DB の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390876"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート

次の表は、Azure Cosmos DB 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="sql-core-api"></a>SQL (Core) API

|**テンプレート**|**説明**|
|---|---|
|[Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-resource) | このテンプレートでは、共有データベースのスループットを使用する 2 つのコンテナーと専用スループットを使用する 1 つのコンテナーのある 2 つのリージョンに、SQL (Core) API アカウントを作成します。 スループットは、スループットのプロパティ値が更新されたテンプレートを再送信することで更新できます。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-sql-with-resource-manager.md#create-sproc) | このテンプレートは、コンテナーのストアド プロシージャ、トリガー、および UDF を使用して、2 つのリージョンに SQL (Core) API アカウントを作成します。 |
|[既存の Azure Cosmos アカウントに対するプライベート エンドポイントを作成する](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos SQL API アカウントのプライベート エンドポイントを作成します。 |

## <a name="mongodb-api"></a>MongoDB API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、データベース、コレクションを作成する](manage-mongodb-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで MongoDB 用 Azure Cosmos DB の API を使用してアカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのコンテナーがあります。 |

## <a name="cassandra-api"></a>Cassandra API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、キースペース、テーブルを作成する](manage-cassandra-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Cassandra API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのテーブルがあります。 |

## <a name="gremlin-api"></a>Gremlin API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、データベース、グラフを作成する](manage-gremlin-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Gremlin API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのグラフがあります。 |

## <a name="table-api"></a>テーブル API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、テーブルを作成する](manage-table-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Table API アカウントを作成します。 Azure Cosmos アカウントには 1 つのテーブルが作成されます。 |

> [!TIP]
> Table API を使用する際に共有スループットを有効にするには、Azure portal でアカウント レベルのスループットを有効にします。

リファレンス ドキュメントについては、[Azure Cosmos DB 用 Azure Resource Manager リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。
