---
title: PostgreSQL ポートの変更
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがリッスンしているポートを変更します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 516c296bc18a3a38b0a6bbe303313119fe2bce1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561804"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>サーバー グループがリッスンしているポートの変更 

ポートを変更するには、サーバー グループを編集します。 たとえば、次のコマンドを実行します。

```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

サーバー グループの名前が _postgres01_ である場合に、これがポート _866_ でリッスンするようにするとします。 次のコマンドを実行します。

```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace arc --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>ポートが変更されたことを確認する

ポートが変更されたことを確認するには、次のコマンドを実行して、サーバー グループの構成を表示します。

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

そのコマンドの出力で、サーバー グループの仕様の "service" セクション内にある項目 "port" について表示されているポート番号を確認します。

または、サーバー グループの仕様の "status" セクション内にある項目 `externalEndpoint` で、構成したポート番号の前に IP アドレスがあることを確認できます。

実例として、上記の例を続行する場合は、次のコマンドを実行します。

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

コマンドによって、ポート 866 が返されます。

```output
"services": {
      "primary": {
        "port": 866,
        "type": "LoadBalancer"
      }
    }
```

さらに、`primaryEndpoint` の値を書き留めます。

```output
"primaryEndpoint": "12.345.67.890:866",
```

## <a name="next-steps"></a>次のステップ
- [サーバー グループに接続する方法](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)を参照します。
- このドキュメントの、方法、管理、構成、スケーリングに関する各セクションで、サーバー グループの他の側面を構成する方法について参照します。
