---
title: Azure CLI を使用してサーバーレス関数アプリを作成する
description: Azure CLI を使用して Azure でのサーバーレス実行用の関数アプリを作成する
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1d4508572d284347af9df961b324f44ec1518f88
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786119"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>サーバーレス コード実行用の関数アプリを作成する 

この Azure Functions サンプル スクリプトでは、関数のコンテナーである関数アプリを作成します。 関数アプリは、[従量課金プラン](../consumption-plan.md)を使用して作成されます。このプランは、イベント ドリブンのサーバーレス ワークロードに最適です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、[従量課金プラン](../consumption-plan.md)を使用して Azure Function App が作成されます。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Azure Storage アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Function App を作成します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
