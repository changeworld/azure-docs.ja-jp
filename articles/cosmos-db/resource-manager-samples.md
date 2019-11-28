---
title: Azure Cosmos DB の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961855"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート

次の表は、Azure Cosmos DB 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="sql-core-api"></a>SQL (Core) API

|**テンプレート**|**説明**|
|---|---|
|[Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-resource) | このテンプレートでは、共有データベースのスループットを使用する 2 つのコンテナーと専用スループットを使用する 1 つのコンテナーのある 2 つのリージョンに、SQL (Core) API アカウントを作成します。 スループットは、スループットのプロパティ値が更新されたテンプレートを再送信することで更新できます。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-sql-with-resource-manager.md#create-sproc) | このテンプレートは、コンテナーのストアド プロシージャ、トリガー、および UDF を使用して、2 つのリージョンに SQL (Core) API アカウントを作成します。 |

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
