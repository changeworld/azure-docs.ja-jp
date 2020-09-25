---
title: Azure Arc データ コントローラーを削除する
description: Azure Arc データ コントローラーを削除する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931295"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc データ コントローラーを削除する

以下の記事では、Azure Arc データ コントローラーを削除する方法について説明します。

先に進む前に、データ コントローラーに作成されているすべてのデータ サービスを、次のようにして削除してください。

## <a name="log-in-to-the-data-controller"></a>データ コントローラーにログインする

削除するデータ コントローラーにログインします。

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>既存のデータ サービスを表示して削除する

次のコマンドを実行して、SQL マネージド インスタンスが作成されているかどうかを確認します。

```
azdata arc sql mi list
```

上の一覧にある SQL マネージド インスタンスごとに、次のように削除コマンドを実行します。

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

同様に、PostgreSQL Hyperscale インスタンスを確認するには、次のように実行します。

```
azdata arc postgres server list
```

また、各 PostgreSQL Hyperscale インスタンスに対して、次のように削除コマンドを実行します。
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>コントローラーを削除する

すべての SQL マネージド インスタンスと PostgreSQL Hyperscale インスタンスを削除した後、次のようにしてデータ コントローラーを削除できます。

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCC を削除する (Red Hat OpenShift のみ)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>必要に応じて、Azure Arc データ コントローラーの名前空間を削除します


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>次の手順

[Azure Arc 対応データ サービスとは](overview.md)