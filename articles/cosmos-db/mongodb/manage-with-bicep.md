---
title: Bicep を使用した Azure Cosmos DB 用の MongoDB API の作成と管理
description: Bicep を使用して、Azure Cosmos DB 用の MongoDB API を作成して構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699775"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>Bicep を使用して Azure Cosmos DB 用の MongoDB API リソースを管理する

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

この記事では、Bicep を使用して、MongoDB API 用の Azure Cosmos DB アカウント、データベース、コレクションのデプロイと管理を行う方法について説明します。

この記事では Gremlin API アカウント用の Bicep サンプルを示します。 [SQL](../sql/manage-with-bicep.md)、[Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[Table](../table/manage-with-bicep.md) API 用の Bicep サンプルもあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースのいずれかを作成するには、次の例を新しい bicep ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md)、[Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>自動スケーリングのプロビジョニング スループットを備える MongoDB API

このテンプレートは、データベース レベルで自動スケーリングのスループットを共有する 2 つのコレクションを含む MongoDB API (3.2、3.6、または 4.0) 用の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える MongoDB API

データベース レベルで 400 RU/秒の標準 (手動) スループットを共有する 2 つのコレクションを含む MongoDB API (3.2、3.6、または 4.0) 用の Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Bicep のドキュメント](../../azure-resource-manager/bicep/index.yml)
* [Bicep ツールのインストール](../../azure-resource-manager/bicep/install.md)
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
  * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください
  * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください
