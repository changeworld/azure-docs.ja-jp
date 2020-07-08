---
title: マネージド リソース グループの取得と VM のサイズ変更 - Azure CLI
description: Azure マネージド アプリケーションのマネージド リソース グループを取得する Azure CLI サンプル スクリプトを紹介します。 このスクリプトでは、VM のサイズを変更します。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f119fe4b4547bda8249a3620baf938dd8b83c235
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056044"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Azure CLI でマネージド リソース グループ内のリソースを取得し、VM のサイズを変更します

このスクリプトはマネージド リソース グループからリソースを取得し、そのリソース グループ内の VM のサイズを変更します。


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | マネージド アプリケーションの一覧を表示します。 クエリの値を指定して結果にフォーカスします。 |
| [az resource list](/cli/azure/resource#az-resource-list) | リソースの一覧を表示します。 リソース グループとクエリの値を指定して結果にフォーカスします。 |
| [az vm resize](/cli/azure/vm#az-vm-resize) | 仮想マシンのサイズを更新します。 |


## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
