---
title: マネージド アプリケーション定義を作成する - Azure CLI
description: マネージド アプリケーションの定義をサブスクリプションに作成する Azure CLI サンプル スクリプトを紹介します。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2430b14ce3a3ba578787cefa85d95475c3e9b920
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775459"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Azure CLI を使用してマネージド アプリケーション定義を作成する

このスクリプトは、マネージド アプリケーション定義をサービス カタログに発行します。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーション定義を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az_managedapp_definition_create) | マネージド アプリケーション定義を作成します。 必要なファイルを含むパッケージを提供します。 |


## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
