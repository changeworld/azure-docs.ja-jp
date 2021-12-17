---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成する
titleSuffix: Azure Arc-enabled data services
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 35a7a184ae3b699d5ed4d25c1cb6b78269dcea11
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279304"
---
# <a name="get-connection-endpoints--create-the-connection-strings-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成します

この記事では、サーバー グループの接続エンドポイントを取得する方法と、アプリケーションまたはツール (あるいはその両方) で使用する接続文字列を作成する方法について説明します。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>接続エンドポイントを取得する:

次のコマンドを実行します。
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace arc --use-k8s
```

エンドポイントの一覧 (PostgreSQL エンドポイント、ログ検索ダッシュボード (Kibana)、およびメトリック ダッシュボード (Grafana)) が返されます。 次に例を示します。 

```output
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@12.345.567.89:5432"
        },
        {
          "description": "Log Search Dashboard",
          "endpoint": "https://23.456.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))"
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://34.567.890.12:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

これらのエンドポイントは次の目的で使用します。

- 接続文字列を作成し、クライアント ツールまたはアプリケーションに接続する
- ブラウザーから Grafana ダッシュボードと Kibana ダッシュボードにアクセスする

たとえば、_PostgreSQL インスタンス_ という名前のエンドポイントを使用して、psql でサーバー グループに接続できます。
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - "_PostgreSQL インスタンス_" という名前のエンドポイントで指定された _postgres_ ユーザーのパスワードは、サーバー グループのデプロイ時に選択したパスワードです。


## <a name="from-cli-with-kubectl"></a>CLI から kubectl を使用する
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

次に例を示します。
```azurecli
kubectl get postgresqls/postgres01 -n arc
```

これらのコマンドでは、次のような出力が生成されます。 この情報を使用して、接続文字列を作成できます。

```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   3/3          12.345.567.89:5432   9d
```

## <a name="form-connection-strings"></a>接続文字列を作成する

サーバー グループには、以下の接続文字列の例を使用します。 必要に応じて、コピー、貼り付け、カスタマイズします。

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>次のステップ
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-in-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する
