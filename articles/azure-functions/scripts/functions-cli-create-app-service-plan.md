---
title: "Azure CLI スクリプト サンプル - App Service プランで Function App を作成する | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - App Service プランで Function App を作成する"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d887999d1118e498293fb13b9612828d914b0ae
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-in-an-app-service-plan"></a>App Service プランで Function App を作成する

このサンプル スクリプトでは、関数のコンテナーである Azure Function App を作成します。 Function App は、専用の App Service プランを使用して作成されるため、サーバーのリソースは Always On が有効になります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "App Service プランで Azure 関数を作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、およびすべての関連リソースを削除できます。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Azure Function App を作成します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
