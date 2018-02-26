---
title: "Azure CLI スクリプト サンプル - App Service プランで Function App を作成する | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - App Service プランで Function App を作成する"
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/22/2018
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: c035eabe966eed97ce8c1df1e4ffba327f07ccac
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>App Service プランで Function App を作成する

この Azure Functions サンプル スクリプトでは、関数のコンテナーである関数アプリを作成します。 作成された関数アプリは専用の App Service プランを使用するため、サーバーのリソースは常に有効になります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、専用の [App Service プラン](../functions-scale.md#app-service-plan)を使用して Azure Function App が作成されます。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Azure Storage アカウントを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#az_appserviceplan_create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_delete) | Azure Function App を作成します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
