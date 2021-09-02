---
title: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
description: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 7254ed303e45c69f291aa5c7a06f63390aaed162
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750267"
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
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
このコマンドで使用できるパラメーターの詳細については、次を実行してください。
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>CLI から kubectl を使用する
次のコマンドのいずれかを実行します。

**Postgres のバージョンに関係なくサーバー グループの一覧を表示するには、以下を実行します。**
```console
kubectl get postgresqls
```
次のような出力が返されます。
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Postgres の特定のバージョンのサーバー グループの一覧を表示するには、以下を実行します。**
```console
kubectl get postgresql-12
```

Postgres のバージョン 11 を実行しているサーバー グループの一覧を表示するには、_postgresql-12_ を _postgresql-11_ に置き換えます。

## <a name="next-steps"></a>次のステップ:

* [接続エンド ポイントを取得し、サーバー グループに接続するための接続文字列を作成する方法についての記事を参照する](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc 対応 PostgreSQL ハイパースケール サーバー グループの構成の表示に関する記事を参照する](show-configuration-postgresql-hyperscale-server-group.md)
