---
title: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
description: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 9b479950bc5abc47cfc0f86a21262511a74f6852
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279305"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する

この記事では、Arc データ コントローラーに作成されているサーバー グループの一覧を取得する方法について説明します。

この一覧を取得するには、Arc データ コントローラーに接続した後、次のいずれかの方法を使用します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>Azure CLI 拡張機能 (az) を使用して CLI から

コマンドの一般的な形式は次のとおりです。
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

次のような出力が返されます。
```console
[
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
]
```
このコマンドで使用できるパラメーターの詳細については、次を実行してください。
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>CLI から kubectl を使用する
次のコマンドのいずれかを実行します。

**Postgres のバージョンに関係なくサーバー グループの一覧を表示するには、以下を実行します。**
```console
kubectl get postgresqls -n <namespace>
```
次のような出力が返されます。
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.67.890:5432   12d
```

## <a name="next-steps"></a>次のステップ:

* [接続エンド ポイントを取得し、サーバー グループに接続するための接続文字列を作成する方法についての記事を参照する](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc 対応 PostgreSQL ハイパースケール サーバー グループの構成の表示に関する記事を参照する](show-configuration-postgresql-hyperscale-server-group.md)
