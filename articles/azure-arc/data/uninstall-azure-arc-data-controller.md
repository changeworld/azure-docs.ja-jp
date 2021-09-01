---
title: Azure Arc データ コントローラーを削除する
description: Azure Arc データ コントローラーを削除する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 26784222d49e6f48ed324ce345dcb1f2ba7d4cf1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741444"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc データ コントローラーを削除する

以下の記事では、Azure Arc データ コントローラーを削除する方法について説明します。

先に進む前に、データ コントローラーに作成されているすべてのデータ サービスを、次のようにして削除してください。

## <a name="list--delete-existing-data-services"></a>既存のデータ サービスを表示して削除する

次のコマンドを実行して、SQL マネージド インスタンスが作成されているかどうかを確認します。

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

上の一覧にある SQL マネージド インスタンスごとに、次のように削除コマンドを実行します。

```azurecli
az sql mi-arc delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az sql mi-arc delete -n sqlinstance1 --k8s-namespace <namespace> --use-k8s
```

同様に、PostgreSQL Hyperscale インスタンスを確認するには、次のように実行します。

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

また、各 PostgreSQL Hyperscale インスタンスに対して、次のように削除コマンドを実行します。

```azurecli
az postgres arc-server delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az postgres arc-server delete -n pg1 --k8s-namespace <namespace> --use-k8s
```

## <a name="delete-controller"></a>コントローラーを削除する

すべての SQL マネージド インスタンスと PostgreSQL Hyperscale インスタンスを削除した後、次のようにしてデータ コントローラーを削除できます。

```azurecli
az arcdata dc delete -n <name> -ns <namespace>
# for example: az arcdata dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCC を削除する (Red Hat OpenShift のみ)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>クラスター レベル オブジェクトを削除する

名前空間スコープ オブジェクトに加えて、CRD、`clusterroles`、`clusterrolebindings` などのクラスター レベル オブジェクトも削除する場合は、次のコマンドを実行します。

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>必要に応じて、Azure Arc データ コントローラーの名前空間を削除します


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>次の手順

[Azure Arc 対応データ サービスとは](overview.md)
