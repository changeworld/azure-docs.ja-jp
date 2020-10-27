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
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320200"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 対応データ サービスのログを取得する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

先に進む前に、次のものが必要です。

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [インストール手順](./install-client-tools.md)。
* Azure Arc 対応データ サービス コントローラーにサインインするための管理者アカウント。

## <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 対応データ サービスのログを取得する

トラブルシューティングのために、すべてのポッドまたは特定のポッドで Azure Arc 対応データ サービスのログを取得できます。 これは、`kubectl logs` コマンドなどの標準の Kubernetes ツールを使用して実行できます。この記事では、すべてのログを一度に取得できる [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ツールを使用します。

1. 管理者アカウントでデータ コントローラーにサインインします。

   ```console
   azdata login
   ```

2. 次のコマンドを実行して、ログをダンプします。

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   次に例を示します。

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

データ コントローラーによって、現在の作業ディレクトリの `logs` という名前のサブディレクトリにログ ファイルが作成されます。 

## <a name="options"></a>オプション

`azdata arc dc debug copy-logs` には、出力を管理するための次のオプションが用意されています。

* `--target-folder` パラメーターを使用して、ログ ファイルを別のディレクトリに出力します。
* `--skip-compress` パラメーターを省略することで、ファイルを圧縮します。
* `--exclude-dumps` を省略することで、メモリ ダンプをトリガーして含めます。 Microsoft サポートがメモリ ダンプを要求していない限り、この方法は推奨されません。 メモリ ダンプを取得するには、データ コントローラーの作成時に、データ コントローラーの `allowDumps` 設定が `true` に設定されている必要があります。
* 名前によって特定のポッド (`--pod`) またはコンテナー (`--container`) のログのみを収集するフィルター処理を行います。
* `--resource-kind` と `--resource-name` パラメーターを渡すことによって、特定のカスタム リソースのログを収集するフィルター処理を行います。 `resource-kind` パラメーターの値は、`kubectl get customresourcedefinition` コマンド で取得できるカスタム リソース定義名のいずれかにする必要があります。

これらのパラメーターを使用して、次の例の `<parameters>` を置き換えることができます。 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

次に例を示します。

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

フォルダー階層の例。 フォルダー階層は、ポッド名、コンテナー、コンテナー内のディレクトリ階層によって整理されます。

```output
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

## <a name="next-steps"></a>次の手順

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)