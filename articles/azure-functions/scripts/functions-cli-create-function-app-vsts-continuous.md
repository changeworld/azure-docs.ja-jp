---
title: Visual Studio Team Services からデプロイされる関数を Azure で作成する | Microsoft Docs
description: Function App の作成と Visual Studio Team Services からの関数コードのデプロイ
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988954"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>関数アプリの作成と Visual Studio Team Services からの関数コードのデプロイ

このトピックでは、Azure Functions を使用して、[消費プラン](../functions-scale.md#consumption-plan)を使用する[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)関数アプリを作成する方法を説明します。 関数アプリは、関数のコンテナーであり、Visual Studio Team Services (VSTS) リポジトリから継続的にデプロイされます。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

このトピックを完了するには、以下が必要です。

* ユーザーの関数アプリ プロジェクトが含まれ、ユーザーが管理アクセス許可を持っている VSTS リポジトリ。
* VSTS リポジトリにアクセスするための[個人用アクセス トークン (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、バージョン 2.0 以降をインストールして使用する必要があります。 Azure CLI のバージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、Visual Studio Team Services から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、ストレージ アカウント、関数アプリ、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | 関数アプリに必要なストレージ アカウントを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で関数アプリを作成します。 |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
