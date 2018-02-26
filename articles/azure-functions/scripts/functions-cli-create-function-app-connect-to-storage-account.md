---
title: "Azure Storage に接続する Azure 関数の作成 | Microsoft Docs"
description: "Azure CLI スクリプトのサンプル - Azure Storage に接続する Azure 関数の作成"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: b4dad0bba7a147f294c5d9ce3a1a1aa8e95058f3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Azure Storage アカウントに接続する関数アプリの作成

この Azure Functions サンプル スクリプトは、関数アプリを作成し、関数を Azure Storage アカウントに接続します。 作成された、接続を含むアプリ設定は、[ストレージのトリガーまたはバインド](..\functions-bindings-storage-blob.md)と共に使用することができます。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで使う場合は、Azure CLI バージョン 2.0 以降を実行していることを確認してください。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、ストレージ接続文字列をアプリ設定に追加します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコマンドを実行して、リソース グループとすべての関連リソースを削除できます。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#az_login) | Azure にログインします。 |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 任意の場所にリソース グループを作成します |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | ストレージ アカウントの作成 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | 新しい Function App を作成します |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | クリーンアップ |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
