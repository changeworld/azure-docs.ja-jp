---
title: Azure CLI のサンプル スクリプト - Azure Redis Cache の詳細を取得 | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure Redis Cache の詳細を取得
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 00ae8e00d456d4660cadf645d4521173d254bbd7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846249"
---
# <a name="get-details-of-an-azure-redis-cache"></a>Azure Redis Cache の詳細を取得

このシナリオでは、プロビジョニング ステータスを含む、Azure Redis Cache インスタンスの詳細を取得する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Redis Cache インスタンスの詳細を取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis の表示](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Azure Redis Cache インスタンスの詳細を取得します。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Redis Cache の CLI サンプル スクリプトは、[Azure Redis Cache のドキュメント](../cli-samples.md)のページにあります。