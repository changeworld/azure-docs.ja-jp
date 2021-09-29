---
title: Azure CLI のサンプル スクリプト - カスタム トピックの作成 | Microsoft Docs
description: この記事では、Azure Event Grid のカスタム トピックの作成方法を示したサンプル Azure CLI スクリプトを紹介しています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 18e33e7ac9c270bfc8736caa0215fdcdd4cf41ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612056"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Azure CLI を使用した Event Grid のカスタム トピックの作成

このスクリプトは、Event Grid のカスタム トピックを作成します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してカスタム トピックを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az_eventgrid_topic_create) | Event Grid のカスタム トピックを作成します。 |


## <a name="next-steps"></a>次のステップ

* サブスクリプションのクエリを実行する方法については、「[Event Grid サブスクリプションのクエリを実行する](../query-event-subscriptions.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
