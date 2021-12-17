---
title: Azure CLI のサンプル スクリプト - Batch ジョブの実行 | Microsoft Docs
description: Azure CLI を使用して、Batch のジョブを作成し、ジョブに一連のタスクを追加する方法について説明します。 この記事では、ジョブとそのタスクを監視する方法も示します。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: Batch, Batch ジョブ, 監視ジョブ, Azure CLI サンプル, Azure CLI コード サンプル, Azure CLI スクリプト サンプル
ms.openlocfilehash: 293e517a528b6a1630ff8597f4fa17c87008a78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595182"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI の例: Azure Batch でのジョブとタスクの実行

このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | コンピューティング ノードのプールを作成します。  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Batch ジョブを作成します。  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | 指定された Batch ジョブにタスクを追加します。  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Batch ジョブのプロパティを更新します。  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | 指定された Batch ジョブの詳細を取得します。  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | 指定された Batch ジョブから、タスクの詳細を取得します。  |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
