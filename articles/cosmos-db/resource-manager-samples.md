---
title: Azure Cosmos DB の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969181"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート

次の表は、Azure Cosmos DB 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="sql-core-api"></a>SQL (Core) API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで SQL (Core) API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのコンテナーがあります。 |
|[データベースのスループット (RU/秒) を更新する](manage-sql-with-resource-manager.md#database-ru-update) | このテンプレートは、SQL (Core) API アカウント内のデータベースのスループットを更新します。 |
|[コンテナーのスループット (RU/秒) を更新する](manage-sql-with-resource-manager.md#container-ru-update) | このテンプレートは、SQL (Core) API アカウント内のコンテナーのスループットを更新します。 |

## <a name="mongodb-api"></a>MongoDB API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、データベース、コレクションを作成する](manage-mongodb-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで MongoDB 用 Azure Cosmos DB の API を使用してアカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのコンテナーがあります。 |
|[データベースのスループット (RU/秒) を更新する](manage-mongodb-with-resource-manager.md#database-ru-update) | このテンプレートは、MongoDB API アカウント内のデータベースのスループットを更新します。 |
|[コレクションのスループット (RU/秒) を更新する](manage-mongodb-with-resource-manager.md#collection-ru-update) | このテンプレートは、MongoDB API アカウント内のコンテナーのスループットを更新します。 |

## <a name="cassandra-api"></a>Cassandra API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、キースペース、テーブルを作成する](manage-cassandra-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Cassandra API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのテーブルがあります。 |
|[キースペースのスループット (RU/秒) を更新する](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | このテンプレートは、Cassandra API アカウント内のキースペースのスループットを更新します。 |
|[テーブルのスループット (RU/秒) を更新する](manage-cassandra-with-resource-manager.md#table-ru-update) | このテンプレートは、Cassandra API アカウント内のテーブルのスループットを更新します。 |

## <a name="gremlin-api"></a>Gremlin API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、データベース、グラフを作成する](manage-gremlin-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Gremlin API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのグラフがあります。 |
|[データベースのスループット (RU/秒) を更新する](manage-gremlin-with-resource-manager.md#database-ru-update) | このテンプレートは、Gremlin API アカウント内のデータベースのスループットを更新します。 |
|[グラフのスループット (RU/秒) を更新する](manage-gremlin-with-resource-manager.md#graph-ru-update) | このテンプレートは、Gremlin API アカウント内のグラフのスループットを更新します。 |

## <a name="table-api"></a>テーブル API

|**テンプレート**|**説明**|
|---| ---|
|[Azure Cosmos アカウント、テーブルを作成する](manage-table-with-resource-manager.md#create-resource) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Table API アカウントを作成します。 Azure Cosmos アカウントには 1 つのテーブルが作成されます。 |
|[テーブルのスループット (RU/秒) を更新する](manage-table-with-resource-manager.md#table-ru-update) | このテンプレートは、Table API アカウント内のテーブルのスループットを更新します。 |

> [!TIP]
> Table API を使用する際に共有スループットを有効にするには、Azure portal でアカウント レベルのスループットを有効にします。

リファレンス ドキュメントについては、[Azure Cosmos DB 用 ARM リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。