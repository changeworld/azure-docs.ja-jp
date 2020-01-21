---
title: Azure Cosmos DB を使用して関数アプリを作成する - Azure CLI
description: Azure CLI スクリプトのサンプル - Azure Cosmos DB に接続する Azure 関数の作成
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 5ee80283ed39789eabb702a48aa97f678a6409f9
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922707"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Azure Cosmos DB に接続する Azure 関数の作成

この Azure Functions サンプル スクリプトは、関数アプリを作成し、関数を Azure Cosmos DB データベースに接続します。 作成された、接続を含むアプリ設定は、[Azure Cosmos DB のトリガーまたはバインド](../functions-bindings-cosmosdb.md)と共に使用することができます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで使う場合は、Azure CLI バージョン 2.0 以降を実行していることを確認してください。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、Cosmos DB エンドポイントとアクセス キーをアプリ設定に追加します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 任意の場所にリソース グループを作成します |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントの作成 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で関数アプリを作成します。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB のデータベースを作成します。 |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| データベース アカウント接続を取得します。 |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| データベースのキーを取得します。 |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 関数アプリのアプリ設定として接続文字列を設定します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。




