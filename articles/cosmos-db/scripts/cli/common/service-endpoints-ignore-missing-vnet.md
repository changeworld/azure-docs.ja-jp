---
title: 仮想ネットワーク サービス エンドポイントを使用する既存の Azure Cosmos アカウントを接続する
description: 仮想ネットワーク サービス エンドポイントを使用する既存の Azure Cosmos アカウントを接続する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: eae343b0ac85f3fdf6d0f6d52c7afbb91f401df4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770770"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Azure CLI を使用して、仮想ネットワーク サービス エンドポイントを使用する既存の Azure Cosmos アカウントを接続する
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.9.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、`ignore-missing-vnet-service-endpoint` パラメーターを使用して、サービス エンドポイント用のサブネットがまだ構成されていない既存の新しい仮想ネットワークに既存の Azure Cosmos アカウントを接続する方法を示すことを目的としています。 これにより、仮想ネットワークのサブネットへの構成が完了する前に、Cosmos アカウントの構成をエラーなしで完了することができます。 サブネットの構成が完了すると、構成されたサブネットを介して Cosmos アカウントにアクセスできるようになります。

> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントの使用方法を紹介しています。 このサンプルを他の API に対して使用するには、次のスクリプトの `enable-virtual-network` パラメーターと `virtual-network-rules` パラメーターをお使いの API 固有のスクリプトに適用します。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Azure 仮想ネットワークを作成します。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Azure 仮想ネットワークのサブネットを作成します。 |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Azure 仮想ネットワークのサブネットが返されます。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB アカウントを作成します。 |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Azure 仮想ネットワークのサブネットを更新します。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB CLI の詳細については、[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)を参照してください。

Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。
