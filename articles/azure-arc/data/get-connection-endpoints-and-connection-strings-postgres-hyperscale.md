---
title: Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成する
titleSuffix: Azure Arc enabled data services
description: Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670343"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Arc 対応 PostgreSQL Hyperscale サーバー グループの接続エンドポイントを取得し、接続文字列を作成する

この記事では、サーバー グループの接続エンドポイントを取得する方法と、アプリケーションまたはツール (あるいはその両方) で使用する接続文字列を作成する方法について説明します。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>接続エンドポイントを取得する:

### <a name="from-cli-with-azdata"></a>CLI から azdata を使用する
#### <a name="1-connect-to-your-arc-data-controller"></a>1.Arc データ コントローラーに接続する:
- Arc データ コントローラーのホストで既にセッションが開かれている場合:次のコマンドを実行します。
```console
azdata login
```

- Arc データ コントローラーのホストでセッションが開かれていない場合: 次のコマンドを実行します。 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2.接続エンドポイントを表示する
次のコマンドを実行します。
```console
azdata arc postgres endpoint list -n <server group name>
```
次に例を示します。
```console
azdata arc postgres endpoint list -n postgres01
```

エンドポイントの一覧が表示されます: アプリケーションを接続し、データベースを使用するために使用する PostgreSQL エンドポイントと、ログ分析と監視のための Kibana と Grafana のエンドポイント。 次に例を示します。 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
これらのエンドポイントは次の目的で使用します。
- 接続文字列を作成し、クライアント ツールまたはアプリケーションに接続する
- ブラウザーから Grafana ダッシュボードと Kibana ダッシュボードにアクセスする

たとえば、_PostgreSQL インスタンス_ という名前のエンドポイントを使用して、psql でサーバー グループに接続できます。 次に例を示します。
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
> - azdata について: 接続に関連付けられているリース時間は約 10 時間です。 その後、再接続する必要があります。 リースの有効期限が切れている場合は、azdata でコマンド (azdata login 以外) を実行しようとすると、次のエラー メッセージが表示されます。_エラー: (401)_ 
> _理由:承認されていない_
> _HTTP 応答ヘッダー:HTTPHeaderDict({'Date':'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length':'0', 'WWW-Authenticate':'_ 
> _基本領域 ="ログイン_ 資格情報が必要です", ベアラー エラー ="invalid_token", error_description="トークンの有効期限が切れています"'})_ これが発生した場合は、上記の説明に従って、azdata に再接続する必要があります。

## <a name="from-cli-with-kubectl"></a>CLI から kubectl を使用する
- サーバー グループの Postgres バージョンが 12 (既定) である場合は、次のコマンドを実行します。
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- サーバー グループの Postgres バージョンが 11 である場合は、次のコマンドを実行します。
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

これらのコマンドでは、次のような出力が生成されます。 この情報を使用して、接続文字列を作成できます。
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>接続文字列を作成する:
サーバー グループの接続文字列の次のテンプレート表を使用します。 次に、必要に応じて、コピー/貼り付けやカスタマイズを行うことができます。

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
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する


