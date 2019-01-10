---
title: Azure CLI を使用した Azure Cosmos DB リソースの管理
description: Azure CLI を使用し、Azure Cosmos DB のアカウント、データベース、コンテナーを作成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d45f5b5c5945796e30c86b2e3ef48d6b8e693b99
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038978"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Azure CLI を使用した Azure Cosmos DB リソースの管理

次のガイドでは、Azure CLI を使用して Azure Cosmos DB のアカウント、データベース、コンテナーの管理を自動化するためのコマンドについて説明します。 コンテナー スループットをスケーリングするためのコマンドも含まれています。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/cosmosdb)で確認できます。 [Azure Cosmos DB 向け Azure CLI サンプル](cli-samples.md)にも、MongoDB、Gremlin、Cassandra、Table API で Cosmos DB のアカウント、データベース、コンテナーを作成し、管理する方法などを含むその他の例があります。

この CLI サンプル スクリプトでは、Azure Cosmos DB の SQL API アカウント、データベース、コンテナーを作成します。  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

米国東部と米国西部で SQL API、セッションの整合性、マルチマスターが有効になっている Azure Cosmos DB アカウントを作成するには、Azure CLI またはクラウド シェルを開き、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>データベースを作成する

Cosmos DB データベースを作成するには、Azure CLI またはクラウド シェルを開き、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>コンテナーを作成する

秒あたり RU が 1000 でパーティション キーを持つ Cosmos DB コンテナーを作成するには、Azure CLI またはクラウド シェルを開き、次のコマンドを実行します。

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>コンテナーのスループットの変更

Cosmos DB コンテナーのスループットを毎秒 400 RU に変更するには、Azure CLI またはクラウド シェルを開き、次のコマンドを実行します。

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>アカウント キーの一覧表示

Azure Cosmos DB アカウントを作成すると、2 つのマスター アクセス キーが生成されます。これらのアクセス キーは、Azure Cosmos DB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、Azure Cosmos DB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。 次のコマンドを実行してアカウントのキーを取得できます。

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>接続文字列の一覧表示

アプリケーションを Cosmos DB アカウントに接続する接続文字列は、次のコマンドで取得できます。

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>アカウント キーの再生成

接続のセキュリティを高めるために、Azure Cosmos DB アカウントのアクセス キーは定期的に変更する必要があります。 一方のアクセス キーを使用して Azure Cosmos DB アカウントへの接続を維持しながら、もう一方のアクセス キーを再生成できるように、2 つのアクセス キーが割り当てられます。

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、次をご覧ください。

- [Azure CLI のインストール](/cli/azure/install-azure-cli)
- [Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB の追加 Azure CLI サンプル](cli-samples.md)
