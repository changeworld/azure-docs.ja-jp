---
title: Azure CLI のサンプル スクリプト - BLOB ストレージ アカウントのサブスクライブ | Microsoft Docs
description: この記事では、Azure Blob Storage アカウントのイベントをサブスクライブする方法を示したサンプル Azure CLI スクリプトを紹介しています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab0995a49f944344b6914bd0e51f1d437dd28c69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766756"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Azure CLI を使用した BLOB ストレージ アカウントのイベントのサブスクライブ

このスクリプトは、BLOB ストレージ アカウントのイベントに対する Event Grid サブスクリプションを作成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid のサブスクリプションを作成する。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 拡張機能のバージョン | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* サブスクリプションのクエリを実行する方法については、「[Event Grid サブスクリプションのクエリを実行する](../query-event-subscriptions.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
