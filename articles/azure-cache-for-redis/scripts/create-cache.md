---
title: Azure Cache for Redis を作成する - Azure CLI
description: この Azure CLI のコード サンプルでは、az redis create コマンドを使用して Azure Cache for Redis インスタンスを作成する方法を示します。
author: curib
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: cauribeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: c0fb70c847929ef404bdb3c2f79ea4e618b74711
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534296"
---
# <a name="create-an-azure-cache-for-redis"></a>Azure Cache for Redis を作成する

このシナリオでは、Azure Cache for Redis の作成方法について説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループと Azure Cache for Redis を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az redis create](/cli/azure/redis) | Azure Cache for Redis インスタンスを作成します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。