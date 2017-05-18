---
title: "Function App の作成と GitHub からの関数コードのデプロイ | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Function App の作成と GitHub からの関数コードのデプロイ"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f87cf7d300b4c2b89ad692aadcda958e9747c7f9
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Function App の作成と GitHub からの関数コードのデプロイ

このサンプル スクリプトでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して関連リソースと共に Function App を作成し、GitHub レポジトリから関数コードをデプロイします。(継続的なデプロイは行いません)。 GitHub から関数コードを継続的に配信する場合は、[Function App の作成と関数と GitHub から継続的なデプロイ](functions-cli-create-function-app-github-continuous.md)に関するページをご覧ください

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、GitHub から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "GitHub からのデプロイで Function App を作成する")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure Function App を作成します。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。

