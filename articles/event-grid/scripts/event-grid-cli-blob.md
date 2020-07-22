---
title: Azure CLI のサンプル スクリプト - BLOB ストレージ アカウントのサブスクライブ | Microsoft Docs
description: この記事では、Azure Blob Storage アカウントのイベントをサブスクライブする方法を示したサンプル Azure CLI スクリプトを紹介しています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: c2dc4780d362f4e98e6b15653123174fca02f03d
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171365"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Azure CLI を使用した BLOB ストレージ アカウントのイベントのサブスクライブ

このスクリプトは、BLOB ストレージ アカウントのイベントに対する Event Grid サブスクリプションを作成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Event Grid のサブスクリプションを作成する。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 拡張機能のバージョン | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* サブスクリプションのクエリを実行する方法については、「[Event Grid サブスクリプションのクエリを実行する](../query-event-subscriptions.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。