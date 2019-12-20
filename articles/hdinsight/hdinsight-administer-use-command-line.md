---
title: Azure CLI を使用した Azure HDInsight クラスターの管理
description: Azure CLI を使用して、Azure HDInsight クラスターを管理する方法について説明します。 クラスターの種類には、Apache Hadoop、Spark、HBase、Storm、Kafka、Interactive Query、および ML サービスがあります。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/06/2019
ms.openlocfilehash: 81bc632f1061f0ee73d2295cafa5f7a8472d20ee
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951804"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLI を使用した Azure HDInsight クラスターの管理

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用して、Azure HDInsight クラスターを管理する方法について説明します。 Azure コマンド ライン インターフェイス (CLI) は、Azure リソースを管理するための、Microsoft のクロスプラットフォーム コマンド ライン エクスペリエンスです。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure CLI。 Azure CLI をインストールしていない場合、手順については「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

* HDInsight 上の Apache Hadoop クラスター。 [Linux での HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

## <a name="connect-to-azure"></a>Azure への接続

Azure サブスクリプションにサインインします。 Azure Cloud Shell を使用する予定の場合は、コード ブロックの右上隅で **[試してみる]** を選択します。 それ以外の場合は、次のコマンドを入力します。

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>クラスターの一覧表示

[az hdinsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) を使用して、クラスターを一覧表示します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` を自分のリソース グループの名前に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>クラスターの表示

[az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) を使用して、指定したクラスターに関する情報を表示します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>クラスターの削除

[az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) を使用して、指定したクラスターを削除します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 これにより、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されることに注意してください。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>クラスターのスケール

[az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) を使用して、指定した HDInsight クラスターのサイズを指定のサイズに変更します。 次のコマンドを編集して、`RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えます。 `TARGET_INSTANCE_COUNT` を、お使いのクラスターに必要なワーカー ノードの数に置き換えます。 クラスターのスケーリングの詳細については、[HDInsight クラスターのスケーリング](./hdinsight-scaling-best-practices.md)に関するページを参照してください。 次のコマンドを入力します。

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>次の手順

この記事では、さまざまな HDInsight クラスター管理タスクを実行する方法について学習しました。 詳細については、次の記事を参照してください。

* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI の概要](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
