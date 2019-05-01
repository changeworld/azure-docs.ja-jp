---
title: Azure CLI スクリプトのサンプル - マネージド アプリケーションをデプロイする | Microsoft Docs
description: Azure CLI スクリプトのサンプル - マネージド アプリケーション定義をデプロイする
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
ms.openlocfilehash: f09d9dcb60370c2cc51f5652def92e5ad9c9b512
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226397"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Azure CLI を使用してサービス カタログのマネージド アプリケーションをデプロイする

このスクリプトは、サービス カタログからマネージド アプリケーション定義をデプロイします。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | マネージド アプリケーションを作成します。 テンプレートの定義 ID とパラメーターを指定します。 |


## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
