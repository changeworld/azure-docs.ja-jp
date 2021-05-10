---
title: GitHub デプロイを使用して Function App を作成する - Azure CLI
description: Azure Functions を使用して、Function App を作成し、GitHub リポジトリから関数コードをデプロイします。
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8abbe30067a488055e59de86372482d99cadaae3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786263"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>GitHub からデプロイされる Function App を Azure で作成する

この Azure Functions のサンプル スクリプトでは、[従量課金プラン](../consumption-plan.md)を使用して、関数アプリと関連リソースが作成されます。 また、GitHub レポジトリから継続的にデプロイされるように関数コードを構成します。 

このサンプルでは、以下が必要です。

* 管理アクセス許可があり、関数コードを含む GitHub レポジトリ。
* GitHub アカウントの[個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、GitHub から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 関数アプリに必要なストレージ アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | サーバーレスの[従量課金プラン](../consumption-plan.md)で関数アプリを作成し、Git リポジトリまたは Mercurial リポジトリにそれを関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
