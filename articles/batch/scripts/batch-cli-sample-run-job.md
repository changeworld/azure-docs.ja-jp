---
title: Azure CLI のサンプル スクリプト - Batch ジョブを実行する
description: このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768168"
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
