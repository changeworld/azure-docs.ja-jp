---
title: Bicep を使用して Azure Cosmos DB Gremlin API を作成し管理する
description: Bicep を使用して、Azure Cosmos DB Gremlin API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: a5ac7ad31cb09ab57948f9a7b1eb86e07ec608a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699270"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-bicep"></a>Bicep を使用して Azure Cosmos DB Gremlin API リソースを管理する

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

この記事では、Bicep を使用して、ご利用の Azure Cosmos DB Gremlin API アカウント、データベース、およびグラフのデプロイと管理を行う方法について説明します。

この記事では Gremlin API アカウントの Bicep サンプルを示します。 [SQL](../sql/manage-with-bicep.md)、[Cassandra](../cassandra/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md)、[Table](../table/manage-with-bicep.md) の API の Bicep サンプルもあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次の例を新しい bicep ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター bicep ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md)、[Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="gremlin-api-with-autoscale-provisioned-throughput"></a>自動スケーリングのプロビジョニング スループットを備える Gremlin API

自動スケーリングのスループットでデータベースとグラフを含む Gremlin API の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="gremlin-api-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Gremlin API

標準プロビジョニング スループットでデータベースとグラフを含む Gremlin API の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/main.bicep":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Bicep のドキュメント](../../azure-resource-manager/bicep/index.yml)
* [Bicep ツールのインストール](../../azure-resource-manager/bicep/install.md)
