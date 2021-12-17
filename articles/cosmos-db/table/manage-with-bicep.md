---
title: Bicep を使用して Azure Cosmos DB Table API を作成し管理する
description: Bicep を使用して Azure Cosmos DB Table API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594454"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>Bicep を使用して Azure Cosmos DB Table API リソースを管理する

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

この記事では、Bicep を使用して、Azure Cosmos DB Table API のアカウントとテーブルのデプロイと管理を行う方法について説明します。

この記事には、Table API アカウントの例だけが含まれています。 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md)、[SQL](../sql/manage-with-bicep.md) の記事の Bicep サンプルもあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次の例を新しい bicep ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター bicep ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md)、[Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md) など、さまざまな方法があります。

> [!TIP]
> Table API を使用する際に共有スループットを有効にするには、Azure portal でアカウント レベルのスループットを有効にします。 アカウント レベルの共有スループットは、Bicep を使用して設定することはできません。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>自動スケーリングのスループットを使用した Table の Azure Cosmos アカウント

スループットが自動スケーリングされる 1 つのテーブルを含む Table API 用の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Table 用の Azure Cosmos アカウント

標準スループットの 1 つのテーブルを含む Table API 用の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Bicep のドキュメント](../../azure-resource-manager/bicep/index.yml)
* [Bicep ツールのインストール](../../azure-resource-manager/bicep/install.md)
