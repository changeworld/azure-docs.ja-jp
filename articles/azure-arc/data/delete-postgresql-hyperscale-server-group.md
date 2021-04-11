---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除する
description: Azure Arc 対応 Postgres Hyperscale サーバー グループを削除する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7932ad3b30910e539acfbff2329a03f80a4d1a0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670360"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除する

このドキュメントでは、Azure Arc のセットアップからサーバーグループを削除する手順について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>サーバー グループを削除する

たとえば、次のセットアップから _postgres01_ インスタンスを削除するとします。

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

削除コマンドの一般的な形式は次のとおりです。
```console
azdata arc postgres server delete -n <server group name>
```
このコマンドを実行すると、サーバー グループの削除を確認するように求められます。 削除を自動化するスクリプトを使用している場合は、--force パラメーターを使用して確認要求をバイパスする必要があります。 たとえば、次のようなコマンドを実行します。 
```console
azdata arc postgres server delete -n <server group name> --force
```

削除コマンドの詳細については、次を実行してください。
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>この例で使用されているサーバー グループを削除する

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes 永続ボリューム要求 (PVC) を再利用する

サーバー グループを削除しても、関連付けられた [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) が削除されるわけではありません。 これは仕様です。 これは、インスタンスが誤って削除された場合に、ユーザーがデータベース ファイルにアクセスできるようにするためです。 PVC の削除は必須ではありません。 ただし、お勧めではあります。 これらの PVC を再利用しない場合、Kubernetes クラスターがディスク領域が不足したと認識するため、最終的にエラーが発生します。 PVC を再利用するには、次の手順を実行します。

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1.削除したサーバー グループの PVC を一覧表示する

PVC を一覧表示するには、次のコマンドを実行します。

```console
kubectl get pvc [-n <namespace name>]
```

PVC の一覧 (特に、削除したサーバー グループの PVC) が返されます。 次に例を示します。

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
このサーバー グループには 8 つの PVC があります。

### <a name="2-delete-each-of-the-pvcs"></a>2.各 PVC を削除する

削除したサーバー グループの PostgreSQL Hyperscale ノード (コーディネーターとワーカー) ごとに、データを削除し、PVC をログに記録します。

このコマンドの一般的な形式は次のとおりです。 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

次に例を示します。

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

これらの各 kubectl コマンドは、PVC が正常に削除されたことを確認します。 次に例を示します。

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> 示しているように、PVC を削除しないと、最終的に Kubernetes クラスターがエラーをスローする状況になる可能性があります。 このようなエラーには、このストレージの問題によってポッドが削除される可能性があるため (通常の Kubernetes 動作)、azdata を使用して Kubernetes クラスターにログインできなくなる場合があります。
>
> たとえば、次のようなメッセージがログに表示されます。  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>次の手順
[Azure Arc 対応 PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md) を作成する
