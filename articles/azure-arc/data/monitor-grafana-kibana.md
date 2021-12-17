---
title: Kibana と Grafana を使用してログとメトリックを表示する
description: Kibana と Grafana を使用してログとメトリックを表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 27f338d093d2084df5ca00b61a8c258d04067b69
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563361"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Kibana と Grafana を使用してログとメトリックを表示する

Web ダッシュボードである Kibana と Grafana は、Azure Arc 対応データ サービスによって使用されている Kubernetes 名前空間に関する分析情報を提供して明らかにするために用意されています。



## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Azure Arc 上の Azure SQL Managed Instance を監視する

Azure Arc 対応 SQL Managed Instance のログと監視ダッシュボードにアクセスするには、次の `azdata` CLI コマンドを実行します。

```azurecl
az sql mi-arc endpoint list -n <name of SQL instance> --use-k8s

```
関連する Grafana ダッシュボードは次のとおりです。

* "Azure SQL Managed Instance メトリック"
* "ホスト ノード メトリック"
* "ホスト ポッド メトリック"


> [!NOTE]
>  ユーザー名とパスワードの入力を求められたら、Azure Arc データ コントローラーの作成時に指定したユーザー名とパスワードを入力します。

> [!NOTE]
>  証明書は自己署名証明書であるため、証明書の警告が表示されます。


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Azure Arc で Azure Database for PostgreSQL Hyperscale を監視する

PostgreSQL Hyperscale のログと監視ダッシュボードにアクセスするには、次の `azdata` CLI コマンドを実行します。

```bash

az postgres arc-server endpoint list -n <name of postgreSQL instance> --k8s-namespace <namespace> --use-k8s

```

関連する postgreSQL ダッシュボードは次のとおりです。

* "Postgres メトリック"
* "Postgres テーブル メトリック"
* "ホスト ノード メトリック"
* "ホスト ポッド メトリック"


## <a name="additional-firewall-configuration"></a>追加のファイアウォール構成

データ コントローラーがデプロイされている場所によっては、Kibana と Grafana の各エンドポイントにアクセスするために、ファイアウォールでポートを開く必要がある場合があります。

以下では、Azure VM に対してこれを行う方法の例を示します。 スクリプトを使用して Kubernetes をデプロイした場合は、これを行う必要があります。

次の手順では、Kibana と Grafana のエンドポイントに対する NSG 規則の作成方法に注目します。

### <a name="find-the-name-of-the-nsg"></a>NSG の名前を検索する

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>NSG 規則を追加する

NSG の名前を取得したら、次のコマンドを使用して規則を追加できます。

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>次のステップ
- [Azure Monitor へのメトリックとログのアップロード](upload-metrics-and-logs-to-azure-monitor.md)を試す
- Grafana について確認する:
   - [作業の開始](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana の基礎](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana のチュートリアル](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Kibana について確認する
   - [はじめに](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana ガイド](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Kibana でのデータ視覚化を使用したダッシュボードのドリルダウンの概要](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana ダッシュボードを作成する方法](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)
