---
title: Bicep を使用して Azure Cosmos DB を作成し管理する
description: Bicep を使用して、Core (SQL) API 用 Azure Cosmos DB を作成して構成する
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699188"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>Bicep を使用して Azure Cosmos DB Core (SQL) API を管理する

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、Bicep を使用して、Azure Cosmos DB アカウント、データベース、コンテナーのデプロイと管理を行う方法について説明します。

この記事では Core (SQL) API アカウントの Bicep サンプルを示します。 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md)、[Table](../table/manage-with-bicep.md) の API の Bicep サンプルもあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新して Bicep ファイルを再デプロイします。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。
> * Azure Cosmos DB リソースの名前を変更することはできません。これは、Azure Resource Manager とリソース URI が連携する方法に違反するためです。
> * データベース単位でスループットをプロビジョニングし、すべてのコンテナー間で共有するには、データベースのオプションのプロパティにスループットの値を設定します。

以下の Azure Cosmos DB リソースを作成するには、次の例を新しい bicep ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター bicep ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md)、[Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>自動スケーリング スループットを備える Azure Cosmos アカウント

一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。データベースとコンテナーは、ほとんどのポリシー オプションが有効になっている自動スケーリング スループット用に構成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>分析ストアを使用する Azure Cosmos アカウント

Analytical TTL が有効なコンテナーと手動または自動スケール スループットのオプションを使用して、1 つのリージョンに Azure Cosmos アカウントを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Azure Cosmos アカウント

一貫性とフェールオーバーのためのオプションを使用して、2 つのリージョンで Azure Cosmos アカウントを作成します。データベースとコンテナーは、ほとんどのポリシー オプションが有効になっている標準スループット用に構成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>サーバー側機能を備える Azure Cosmos DB コンテナー

ストアド プロシージャ、トリガー、ユーザー定義関数を使用して、Azure Cosmos アカウント、データベース、コンテナーを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure AD と RBAC を使用した Azure Cosmos DB アカウント

AAD ID 用の Azure Cosmos アカウント、ネイティブに管理されるロールの定義、ネイティブに管理されるロールの割り当てを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Free レベルの Azure Cosmos DB アカウント

Free レベルの Azure Cosmos アカウントと、最大 25 個のコンテナーで共有できる共有スループットを備えるデータベースを作成します。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Bicep のドキュメント](../../azure-resource-manager/bicep/index.yml)
* [Bicep ツールのインストール](../../azure-resource-manager/bicep/install.md)
