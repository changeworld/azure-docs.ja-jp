---
title: GitHub からデプロイされる関数を Azure で作成する | Microsoft Docs
description: Azure Functions を使用して、関数アプリを作成し、GitHub リポジトリから関数コードをデプロイします。
services: functions
ms.service: functions
keywords: ''
ms.devlang: azurecli
author: syntaxc4
ms.author: cfowler
ms.date: 01/09/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 720ead07aa35540f61f1dc8e15b79ac9d58669be
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
ms.locfileid: "29842730"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>GitHub からデプロイされる関数アプリを Azure で作成する

この Azure Functions のサンプル スクリプトは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して、関数アプリと関連リソースを作成します。 また、GitHub レポジトリから継続的にデプロイされるように関数コードを構成します。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

このサンプルでは、以下が必要です。

* 管理アクセス許可があり、関数コードを含む GitHub レポジトリ。
* GitHub アカウントの[個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、バージョン 2.0 以降をインストールして使用する必要があります。 Azure CLI のバージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、GitHub から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
