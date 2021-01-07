---
title: ファイル共有を Python 関数アプリにマウントする - Azure CLI
description: Azure CLI を使用して、サーバーレス Python 関数アプリを作成し、既存のファイル共有をマウントします。
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89cf1422c70a49497a1bc43e42248612e0c8dd83
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930907"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLI を使用してファイル共有を Python 関数アプリにマウントする

この Azure Functions サンプル スクリプトでは、関数アプリを作成し、Azure Files に共有を作成します。 次に、関数からデータにアクセスできるように、共有をマウントします。  

>[!NOTE]
>作成された関数アプリは、Python バージョン 3.7 上で実行されます。 Azure Functions では、[Python バージョン 3.6 および 3.8](../functions-reference-python.md#python-version) もサポートされます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、[従量課金プラン](../consumption-plan.md)を使用して Azure Function App が作成されます。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Azure Storage アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Function App を作成します。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | ストレージ アカウントに Azure Files 共有を作成します。 | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 共有内にディレクトリを作成します。 |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 共有を関数アプリにマウントします。 |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | 関数アプリにマウントされたファイル共有を表示します。 | 

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
