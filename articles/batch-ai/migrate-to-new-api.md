---
title: 更新された Azure Batch AI API への移行 | Microsoft Docs
description: Azure Batch AI のコードおよびスクリプトを更新し、ワークスペースおよび実験リソースを使用する方法
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407782"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>更新された Batch AI API への移行

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Batch AI REST API バージョン 2018-05-01 および関連する Batch AI SDK とツールでは、重大な変更と新機能が導入されています。

以前のバージョンの Batch AI API を使用している場合、この記事では、コードおよびスクリプトを変更して新しい API で機能するようにする方法を説明します。 

## <a name="whats-changing"></a>変更点

お客様のフィードバックに対応して、ジョブ数の制限を撤廃し、Batch AI リソースの管理を容易にしました。 新しい API は、*ワークスペース*および*実験*の 2 つの新しいリソースを導入します。 関連するトレーニング ジョブを実験の下に集め、関連するすべての Batch AI リソース (クラスター、ファイル サーバー、実験、ジョブ) をワークスペースの下に並べます。  

* **ワークスペース** - すべての種類の Batch AI リソースの最上位コレクション。 クラスターおよびファイル サーバーはワークスペースに格納されます。 ワークスペースは通常、異なるグループまたはプロジェクトに属している作業を分離します。 たとえば、開発ワークスペースとテスト ワークスペースがあるとします。 おそらくサブスクリプションあたり限定された数のワークスペースのみが必要です。 

* **実験** - 同時に照会および管理できる関連するジョブのコレクション。 たとえば、実験を使用して、ハイパー パラメーター チューニング スイープの一環として実行されるすべてのジョブをグループ化します。 

次の図にリソース階層の例を示します。 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>既存のリソースの監視と管理
Batch AI のクラスター、ジョブ、またはファイル サーバーが既に作成済みの各リソース グループでは、Batch AI サービスは、`migrated-<region>` という名前のワークスペース (たとえば、`migrated-eastus`) および `migrated` という名前の実験を作成します。 以前作成したジョブ、クラスター、またはファイル サーバーにアクセスするには、移行したワークスペースおよび実験を使用する必要があります。 

### <a name="portal"></a>ポータル 
ポータルを使用して以前作成したジョブ、クラスター、またはファイル サーバーにアクセスするには、まず `migrated-<region>` ワークスペースを選択します。 ワークスペースを選択した後、クラスターのサイズ変更や削除、ジョブの状態や出力の表示などの操作を実行します。 

### <a name="sdks"></a>SDK 
Batch AI SDK を使用して以前作成したジョブ、クラスター、またはファイル サーバーにアクセスするには、ワークスペース名および経験名のパラメーターを指定します。 

Python SDK を使用する場合、次の例のように関連する変更が表示されます。 変更は他の Batch AI SDK と似ています。 


#### <a name="get-old-cluster"></a>以前のクラスターの取得 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>以前のクラスターの削除 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>以前のファイル サーバーの取得 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>以前のファイル サーバーの削除  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>以前のジョブの取得 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>以前のジョブの削除

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Azure CLI を使用して以前作成したジョブ、クラスター、またはファイル サーバーにアクセスする場合、`-w` パラメーターおよび `-e` パラメーターを使用してワークスペース名および経験名を指定します。 


#### <a name="get-old-cluster"></a>以前のクラスターの取得

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>以前のクラスターの削除 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>以前のファイル サーバーの取得

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>以前のファイル サーバーの削除 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>以前のジョブの取得

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>以前のジョブの削除 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Batch AI リソースの作成 
 
既存の Batch AI SDK のいずれかを使用している場合、これを継続して使用して Batch AI リソース (ジョブ、クラスター、またはファイル サーバー) を作成できます。コードを変更する必要はありません。 ただし、新しい SDK にアップグレードした後、次の変更を行う必要があります。
 
### <a name="create-workspace"></a>ワークスペースの作成 
シナリオに応じて、ポータルまたは CLI を使用してワークスペースを手動で 1 回作成できます。 CLI を使用している場合、次のコマンドを使用してワークスペースを作成します。 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>実験の作成 


シナリオに応じて、ポータルまたは CLI を使用して実験を手動で 1 回作成できます。 CLI を使用している場合、次のコマンドを使用して実験を作成します。 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>クラスター、ファイル サーバー、およびジョブの作成
 
ポータルを使用してジョブ、クラスター、またはファイル サーバーを作成する場合、ポータルの指示に従ってこれらを作成し、ワークスペース名および実験名のパラメーターを指定できます。

更新された SDK を使用してジョブ、クラスター、またはファイル サーバーを作成するには、ワークスペース名パラメーターを指定します。 Python SDK を使用する場合、次の例のように関連する変更が表示されます。 *workspace_name* および *experiment_name* を以前作成したワークスペースおよび実験に置き換えます。 変更は他の Batch AI SDK と似ています。 


#### <a name="create-cluster"></a>クラスターの作成 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>ファイル サーバーの作成 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>ジョブを作成する 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>次の手順

* Batch AI API リファレンスを参照してください:[CLI](/cli/azure/batchai)、[.NET](/dotnet/api/overview/azure/batchai)、[Java](/java/api/overview/azure/batchai)、[Node.js](/javascript/api/overview/azure/batchai)、[Python](/python/api/overview/azure/batchai)、[REST](/rest/api/batchai)