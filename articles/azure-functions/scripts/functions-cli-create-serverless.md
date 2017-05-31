---
title: "Azure CLI スクリプト サンプル - サーバーレス実行用の Function App を作成する | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - サーバーレス実行用の Function App を作成する"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a9ab21a4eb6839006c4a7eca2037308646180010
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>サーバーレス実行用の Function App を作成する

このサンプル スクリプトでは、関数のコンテナーである Azure Function App を作成します。 Function App は、イベント ドリブンのサーバーレス ワークロードに最適な[従量課金プラン](../functions-scale.md#consumption-plan)で作成されます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して Azure Function App が作成されます。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "従量課金プランで Azure 関数を作成する")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#create) | Azure Storage アカウントを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Azure 関数を作成します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。

