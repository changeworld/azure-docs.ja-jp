---
title: Azure CLI スクリプト サンプル - Azure Cache for Redis の詳細を取得する | Microsoft Docs
description: Azure CLI スクリプト サンプル - Azure Cache for Redis の詳細を取得します
services: azure-cache-for-redis
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
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749161"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Azure Cache for Redis の詳細を取得する

このシナリオでは、プロビジョニング状態など、Azure Cache for Redis インスタンスの詳細を取得する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Cache for Redis インスタンスの詳細を取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Azure Cache for Redis インスタンスの詳細を取得します。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。
