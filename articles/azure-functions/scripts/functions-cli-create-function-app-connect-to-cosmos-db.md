---
title: "Azure DocumentDB に接続する Azure 関数の作成 | Microsoft Docs"
description: "Azure CLI スクリプトのサンプル - Azure DocumentDB に接続する Azure 関数の作成"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 46e0fe827b7b34010d14a31ff377d4854ec62f6e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Azure Cosmos DB に接続する Azure 関数の作成

このサンプル スクリプトでは、Azure Function App を作成し、Azure Cosmos DB データベースに接続します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Azure Cosmos DB に接続する Azure 関数の作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループとすべての関連リソースを削除できます。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Azure にログインします。 |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 任意の場所にリソース グループを作成します |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | ストレージ アカウントの作成 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 新しい Function App を作成します |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | DocumentDB データベースを作成します |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | クリーンアップ |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。





