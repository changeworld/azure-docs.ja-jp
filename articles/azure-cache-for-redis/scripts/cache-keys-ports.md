---
title: Azure CLI スクリプトの例 - Azure Cache for Redis のホスト名、ポート、およびキーを取得する | Microsoft Docs
description: Azure CLI スクリプトの例 - Azure Cache for Redis インスタンスのホスト名、ポート、およびキーを取得する
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 0c6f7b637e56d2bf39d8f03122ccb28bd7b1c773
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096747"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Azure Cache for Redis のホスト名、ポート、およびキーを取得する

このシナリオでは、Azure Cache for Redis インスタンスへの接続に使用するホスト名、ポートおよびキーを取得する方法について学習します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Cache for Redis インスタンスのホスト名、キー、およびポートを取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis の表示](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Azure Cache for Redis インスタンスの詳細を取得します。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Azure Cache for Redis インスタンスのアクセス キーを取得します。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。