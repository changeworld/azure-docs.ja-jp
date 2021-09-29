---
title: Azure CLI のサンプル スクリプト - カスタム トピックのサブスクライブ | Microsoft Docs
description: この記事では、カスタム トピックの Event Grid イベントをサブスクライブする方法を示したサンプル Azure CLI スクリプトを紹介しています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a36f39bb4e14e58ab2c21dc96079a899686d5a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595106"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-azure-cli"></a>Azure CLI を使用したカスタム トピックのイベントのサブスクライブ

このスクリプトは、カスタム トピックのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

プレビュー版サンプル スクリプトには、Event Grid 拡張機能が必要です。 インストールするには、`az extension add --name eventgrid` を実行します。

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.sh "Subscribe to custom topic")]

## <a name="sample-script---preview-extension"></a>サンプル スクリプト - プレビュー版拡張機能

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.sh "Subscribe to custom topic")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid のサブスクリプションを作成する。 |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - 拡張機能のバージョン | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* サブスクリプションのクエリを実行する方法については、「[Event Grid サブスクリプションのクエリを実行する](../query-event-subscriptions.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
