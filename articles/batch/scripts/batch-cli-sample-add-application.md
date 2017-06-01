---
title: "Azure CLI スクリプト サンプル - バッチへのアプリケーションの追加 |Microsoft Docs"
description: "Azure CLI スクリプト サンプル - バッチへのアプリケーションの追加"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5d057eaf32867aedc95d58c5185e2be1f9385ec0
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Azure CLI を使用した Azure Batch へのアプリケーションの追加

このスクリプトでは、Azure Batch プールまたはタスクで使用するアプリケーションを設定する方法について説明します。 アプリケーションを設定するには、実行ファイルをすべての依存関係と共に .zip ファイルにパッケージ化します。 この例では、実行可能な zip ファイル名を 'my-application-exe.zip' とします。

## <a name="prerequisites"></a>前提条件

- Azure CLI をまだインストールしていない場合は、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従ってインストールします。
- Batch アカウントをまだお持ちでない場合は、作成します。 アカウント作成のサンプル スクリプトについては、「[Azure CLI で Batch アカウントを作成する](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)」を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "アプリケーションの追加")]

## <a name="clean-up-application"></a>アプリケーションのクリーンアップ

上記のサンプル スクリプトを実行したら、次のコマンドを実行して、アプリケーションとアップロードされたすべてのアプリケーション パッケージを削除します。

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、アプリケーションを作成し、アプリケーション パッケージをアップロードします。
表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | アプリケーションを作成します。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | アプリケーションのプロパティを更新します。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | アプリケーション パッケージを指定されたアプリケーションに追加します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。

