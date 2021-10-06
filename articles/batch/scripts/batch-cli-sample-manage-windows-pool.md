---
title: Azure CLI のサンプル スクリプト - Batch での Windows プール | Microsoft Docs
description: Azure Batch で Windows 計算ノードのプールを作成して管理するために、Azure CLI で使用できるコマンドの一部について説明します。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: windows プール, azure cli サンプル, azure cli コード サンプル, azure cli スクリプト サンプル
ms.openlocfilehash: 6c98d5ece314f6aa8603db73a0a5c0c9a420efde
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595166"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI の例: Azure Batch での Windows プールの作成と管理

このスクリプトでは、Azure Batch で Windows コンピューティング ノードのプールを作成して管理するために、Azure CLI で使用できるコマンドの一部を示します。 Windows プールは、クラウド サービスの構成と仮想マシンの構成という 2 つの方法で構成できます。 この例では、クラウド サービスの構成を使用して Windows プールを作成する方法を示します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch アカウントを作成します。 |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。 |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | コンピューティング ノードのプールを作成します。  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | プールのプロパティを更新します。  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | プールで自動スケーリングを有効にし、式を適用します。  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | プールのプロパティを表示します。  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | プールで自動スケーリングを無効にします。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
