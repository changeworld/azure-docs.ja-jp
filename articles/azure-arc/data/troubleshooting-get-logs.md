---
title: ログを取得して Azure Arc 対応データ コントローラーのトラブルシューティングを行う
description: サービス ログを取得して、Azure Arc 対応データ コントローラーのトラブルシューティングを行います。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931298"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 対応データ サービスのログを取得する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

Azure Arc 対応データ サービスのログを取得するには、Azure Data CLI ツールが必要となります。 [インストール手順](./install-client-tools.md)

Azure Arc 対応データ サービスのコントローラー サービスに管理者としてログインできる必要があります。

## <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 対応データ サービスのログを取得する

トラブルシューティングのために、すべてのポッドまたは特定のポッドで Azure Arc 対応データ サービスのログを取得できます。  これを行うには、`kubectl logs` コマンドなどの標準の Kubernetes ツールを使用するか、この記事のように Azure Data CLI ツールを使用できます。Azure Data CLI ツールを使用すると、すべてのログを一度に簡単に取得できます。

まず、データ コントローラーにログインしていることを確認します。

```console
azdata login
```

その後、次のコマンドを実行してログをダンプします。
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

既定では、ログ ファイルは現在の作業ディレクトリの "logs" という名前のサブディレクトリに作成されます。  `--target-folder` パラメーターを使用すると、ログ ファイルを別のディレクトリに出力できます。

`--skip-compress` パラメーターを省略すると、ファイルを圧縮することができます。

`--exclude-dumps` を省略すると、メモリ ダンプをトリガーして含めることができますが、Microsoft サポートがメモリ ダンプを要請している場合を除き、これはお勧めしません。  メモリ ダンプを取得するには、データ コントローラーの作成時に、データ コントローラーの `allowDumps` 設定が `true` に設定されている必要があります。

必要に応じて、フィルターを選択し、名前によって特定のポッド (`--pod`) またはコンテナー (`--container`) のログのみを収集することもできます。

また、`--resource-kind` と `--resource-name` パラメーターを渡すことによって、特定のカスタム リソースのログを収集するフィルター処理を選択することもできます。  `resource-kind` パラメーターの値は、コマンド `kubectl get customresourcedefinition` で取得できるカスタム リソース定義名のいずれかにする必要があります。

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

フォルダー階層の例。  フォルダー階層は、ポッド名、コンテナー、コンテナー内のディレクトリ階層によって整理されていることに注意してください。

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```