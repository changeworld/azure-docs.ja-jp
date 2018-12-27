---
title: Azure CLI スクリプトのサンプル - マネージド アプリケーション定義を作成する | Microsoft Docs
description: Azure CLI スクリプトのサンプル - マネージド アプリケーション定義を作成する
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
ms.openlocfilehash: 1cc407e07aee307b00116aad1c44bf8e2b97e33e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432475"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Azure CLI を使用してマネージド アプリケーション定義を作成する

このスクリプトは、マネージド アプリケーション定義をサービス カタログに発行します。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーション定義を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | マネージド アプリケーション定義を作成します。 必要なファイルを含むパッケージを提供します。 |


## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
