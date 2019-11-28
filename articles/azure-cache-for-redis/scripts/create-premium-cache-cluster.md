---
title: Azure CLI スクリプト サンプル - クラスタリングを使用して Premium Azure Cache for Redis を作成する
description: Azure CLI スクリプト サンプル - クラスタリングを使用して Premium レベルの Azure Cache for Redis を作成します
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 1813bf7112969436012627147b94f656537029d5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122494"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>クラスタリングを使用する Premium Azure Cache for Redis の作成

このシナリオでは、クラスタリングが有効でシャードが 2 つある、6 GB の Premium レベル Azure Cache for Redis を作成する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループと、クラスタリングが有効な Premium レベルの Azure Cache for Redis を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | Azure Cache for Redis インスタンスを作成します。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。
