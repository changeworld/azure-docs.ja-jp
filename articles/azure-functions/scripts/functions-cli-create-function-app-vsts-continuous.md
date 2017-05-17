---
title: "Function App の作成と Visual Studio Team Services からの関数コードのデプロイ | Microsoft Docs"
description: "Function App の作成と Visual Studio Team Services からの関数コードのデプロイ"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/28/2017
ms.topic: functions
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0ed5d2292180a1831255d45c13f4195120370c34
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="create-an-app-service"></a>App Service の作成

このシナリオでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して関連リソースと共に Function App を作成する方法と Visual Studio Team Services (VSTS) レポジトリから関数コードを継続的にデプロイする方法について説明します。 このサンプルでは、次のものが必要になります。

* 管理アクセス許可があり、関数コードを含む VSTS レポジトリ。
* GitHub アカウントの[個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、DocumentDB、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
