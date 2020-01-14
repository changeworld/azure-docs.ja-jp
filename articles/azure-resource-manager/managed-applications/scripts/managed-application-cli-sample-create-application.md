---
title: Azure CLI スクリプト サンプル - マネージド アプリケーションをデプロイする
description: Azure マネージド アプリケーションの定義をサブスクリプションにデプロイする Azure CLI サンプル スクリプトを紹介します。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 346ea59209bc2f74970e708c947f5caa158a0338
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648789"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Azure CLI を使用してサービス カタログのマネージド アプリケーションをデプロイする

このスクリプトは、サービス カタログからマネージド アプリケーション定義をデプロイします。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | マネージド アプリケーションを作成します。 テンプレートの定義 ID とパラメーターを指定します。 |


## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
