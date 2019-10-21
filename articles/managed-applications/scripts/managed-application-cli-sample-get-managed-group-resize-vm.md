---
title: Azure CLI スクリプトのサンプル - マネージド リソース グループを取得し、VM のサイズを変更する | Microsoft Docs
description: Azure マネージド アプリケーションのマネージド リソース グループを取得する Azure CLI サンプル スクリプトを紹介します。 このスクリプトでは、VM のサイズを変更します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 7218e2fd033614b5e9f77cca34a5db82e3571094
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330140"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Azure CLI でマネージド リソース グループ内のリソースを取得し、VM のサイズを変更します

このスクリプトはマネージド リソース グループからリソースを取得し、そのリソース グループ内の VM のサイズを変更します。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | マネージド アプリケーションの一覧を表示します。 クエリの値を指定して結果にフォーカスします。 |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | リソースの一覧を表示します。 リソース グループとクエリの値を指定して結果にフォーカスします。 |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | 仮想マシンのサイズを更新します。 |


## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
