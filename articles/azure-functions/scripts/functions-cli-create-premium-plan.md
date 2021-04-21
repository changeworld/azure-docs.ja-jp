---
title: Premium プランの Function App を作成する - Azure CLI
description: Azure CLI を使用して Azure のスケーラブル Premium プランの Function App を作成する
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d9f72fa433364f8d71ba44207d570bb827cd243
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786213"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Premium プランの Function App を作成する - Azure CLI

この Azure Functions サンプル スクリプトでは、関数のコンテナーである関数アプリを作成します。 作成される関数アプリは、[スケーラブル Premium プラン](../functions-premium-plan.md)を使用します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、[Premium プラン](../functions-premium-plan.md)を使用する関数アプリを作成します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Azure Storage アカウントを作成します。 |
| [az functionapp plan create](/cli/azure/functionapp/plan#az_functionapp_plan_create) | [特定の SKU](../functions-premium-plan.md#available-instance-skus) で Premium プランを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | App Service プランで関数アプリを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
