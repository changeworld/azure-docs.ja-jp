---
title: Azure CLI スクリプト サンプル - クラスタリングを使用して Premium Azure Cache for Redis を作成する | Microsoft Docs
description: Azure CLI スクリプト サンプル - クラスタリングを使用して Premium レベルの Azure Cache for Redis を作成します
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 7ecae6711950ac180d8126060e9bc5a8e71e71d6
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696100"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>クラスタリングを使用する Premium Azure Cache for Redis の作成

このシナリオでは、クラスタリングが有効でシャードが 2 つある、6 GB の Premium レベル Azure Cache for Redis を作成する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループと、クラスタリングが有効な Premium レベルの Azure Cache for Redis を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | Azure Cache for Redis インスタンスを作成します。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。
