---
title: Azure Cosmos DB の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080577"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート

次の表は、Azure Cosmos DB 用の Azure Resource Manager テンプレートのリンク一覧です。

|**[API の種類]** | **サンプルへのリンク**| **説明** |
|---|---| ---|
|コア (SQL) API| [Azure Cosmos DB アカウントを作成する (マルチマスター)](manage-sql-with-resource-manager.md) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで SQL API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのコンテナーがあります。 |
| MongoDB API | [Azure Cosmos DB アカウントを作成する (マルチマスター)](manage-mongodb-with-resource-manager.md) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで MongoDB 用 Azure Cosmos DB の API を使用して SQL API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのコンテナーがあります。 |
| Cassandra API | [Azure Cosmos DB アカウントを作成する (マルチマスター)](manage-cassandra-with-resource-manager.md) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Cassandra API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのテーブルがあります。 |
| Gremlin API| [Azure Cosmos DB アカウントを作成する (マルチマスター)](manage-gremlin-with-resource-manager.md) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Gremlin API アカウントを作成します。 Azure Cosmos アカウントには、データベース レベルのスループットを共有する 2 つのグラフがあります。 |
| テーブル API | [Azure Cosmos DB アカウントを作成する (マルチマスター)](manage-table-with-resource-manager.md) | このテンプレートは、マルチマスターを有効にした 2 つのリージョンで Table API アカウントを作成します。 Azure Cosmos アカウントには 1 つのテーブルが作成されます。 |

> [!TIP]
> Table API を使用する際に共有スループットを有効にするには、Azure portal でアカウント レベルのスループットを有効にします。

リファレンス ドキュメントについては、[Azure Cosmos DB 用 ARM リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。