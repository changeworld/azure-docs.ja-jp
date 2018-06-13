---
title: Azure CLI スクリプトのサンプル - マネージ アプリケーションをデプロイする | Microsoft Docs
description: Azure CLI スクリプトのサンプル - マネージ アプリケーション定義をデプロイする
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
ms.openlocfilehash: 3090284a89cfeb1fe8b6446e55e2731e1155167f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847218"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Azure CLI を使用してサービス カタログのマネージ アプリケーションをデプロイする

このスクリプトは、サービス カタログからマネージ アプリケーション定義をデプロイします。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージ アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | マネージ アプリケーションを作成します。 テンプレートの定義 ID とパラメーターを指定します。 |


## <a name="next-steps"></a>次の手順

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
