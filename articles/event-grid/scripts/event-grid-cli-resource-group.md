---
title: Azure CLI のサンプル スクリプト - リソース グループのサブスクライブ | Microsoft Docs
description: この記事では、リソース グループの Azure Event Grid イベントをサブスクライブする方法を示したサンプル Azure CLI スクリプトを紹介しています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/22/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: a72e7b1748f7e7fb96e5b282ea299a60fe5deb1d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467399"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Azure CLI を使用したリソース グループのイベントのサブスクライブ

このスクリプトは、リソース グループのイベントに対する Event Grid サブスクリプションを作成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

プレビュー版サンプル スクリプトには、Event Grid 拡張機能が必要です。 インストールするには、`az extension add --name eventgrid` を実行します。

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>サンプル スクリプト - プレビュー版拡張機能

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid のサブスクリプションを作成する。 |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - 拡張機能のバージョン | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* サブスクリプションのクエリを実行する方法については、「[Event Grid サブスクリプションのクエリを実行する](../query-event-subscriptions.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
