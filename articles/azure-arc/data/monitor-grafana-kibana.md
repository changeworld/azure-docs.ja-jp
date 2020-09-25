---
title: Kibana と Grafana を使用してログとメトリックを表示する
description: Kibana と Grafana を使用してログとメトリックを表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d876862d8f41ab8df646bef051629fd45c4d4601
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931495"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Kibana と Grafana を使用してログとメトリックを表示する

Web ダッシュボードである Kibana と Grafana は、Azure Arc 対応データ サービスによって使用されている Kubernetes 名前空間に関する分析情報を提供して明らかにするために用意されています。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>クラスターの IP アドレスを取得する

ダッシュボードにアクセスするには、クラスターの IP アドレスを取得する必要があります。 正しい IP アドレスを取得する方法は、選択した Kubernetes のデプロイ方法によって異なります。 以下のオプションの手順に従って、適切なものを見つけてください。

### <a name="azure-virtual-machine"></a>Azure 仮想マシン

パブリック IP アドレスを取得するには、次のコマンドを使用します。

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm クラスター

クラスターの IP アドレスを取得するには、次のコマンドを使用します。

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS または他の負荷分散クラスター

AKS または他の負荷分散クラスターで環境を監視するには、管理プロキシサービスの IP アドレスを取得する必要があります。 **外部 IP** アドレスを取得するには、次のコマンドを使用します。

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>追加のファイアウォール構成

Kibana と Grafana のエンドポイントにアクセスするには、ファイアウォールでポートを開くことが必要な場合があります。

以下では、Azure VM に対してこれを行う方法の例を示します。 スクリプトを使用して Kubernetes をデプロイした場合は、これを行う必要があります。

次の手順では、Kibana と Grafana のエンドポイントに対する NSG 規則の作成方法に注目します。

### <a name="find-the-name-of-the-nsg"></a>NSG の名前を検索する

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>NSG 規則を追加する

NSG の名前を取得したら、次のコマンドを使用して規則を追加できます。

```console
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Azure Arc 上の Azure SQL Managed Instance を監視する

IP アドレスを取得し、ポートを公開したので、Grafana と Kibana にアクセスできるはずです。

> [!NOTE]
>  ユーザー名とパスワードの入力を求められたら、Azure Arc データ コントローラーの作成時に指定したユーザー名とパスワードを入力します。

> [!NOTE]
>  プレビューで使用される証明書は自己署名証明書であるため、証明書の警告が表示されます。

Azure SQL Managed Instance のログと監視のダッシュボードにアクセスするには、次の URL パターンを使用します。

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

関連するダッシュボードは次のとおりです。

* "Azure SQL Managed Instance メトリック"
* "ホスト ノード メトリック"
* "ホスト ポッド メトリック"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Azure Database for PostgreSQL Hyperscale を監視する - Azure Arc

PostgreSQL Hyperscale のログと監視のダッシュボードにアクセスするには、次の URL パターンを使用します。

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

関連するダッシュボードは次のとおりです。

* "Postgres メトリック"
* "Postgres テーブル メトリック"
* "ホスト ノード メトリック"
* "ホスト ポッド メトリック"

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

