---
title: Bicep を使用して Azure Cosmos DB Cassandra API を作成し管理する
description: Bicep を使用して、Azure Cosmos DB Cassandra API を作成し構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0433f6830100fc23420006ec2b980b7ccf7f9692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594490"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-bicep"></a>Bicep を使用して Azure Cosmos DB Cassandra API リソースを管理する

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事では、Bicep を使用して、ご利用の Azure Cosmos DB Cassandra API アカウント、キースペース、およびテーブルのデプロイと管理を行う方法について説明します。

この記事では Cassandra API アカウントの Bicep サンプルを示します。 [SQL](../sql/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md)、[Table](../table/manage-with-bicep.md) の API の Bicep サンプルもあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次の例を新しい bicep ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター bicep ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md)、[Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="cassandra-api-with-autoscale-provisioned-throughput"></a>自動スケーリングのプロビジョニング スループットを備える Cassandra API

一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。キースペースとテーブルは自動スケーリングのスループット用に構成されています。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="cassandra-api-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Cassandra API

一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。キースペースとテーブルは標準スループット用に構成されています。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra/main.bicep":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Bicep のドキュメント](../../azure-resource-manager/bicep/index.yml)
* [Bicep ツールのインストール](../../azure-resource-manager/bicep/install.md)
