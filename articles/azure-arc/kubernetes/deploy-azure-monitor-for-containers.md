---
title: コンテナー用として Azure Arc と Azure Monitor をオンボードする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: コンテナー用として Azure Arc と Azure Monitor をオンボードする
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680737"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Arc と共にコンテナー用として Azure Monitor をオンボードする (プレビュー)

Azure Arc が有効になっている Kubernetes クラスターに [Azure Monitor が有効になっているコンテナー](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)をオンボードします。

## <a name="before-you-begin"></a>開始する前に

* [Kubernetes バージョン](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* (master および worker) ノード向け Linux ディストリビューション – Ubuntu (18.04 LTS と 16.04 LTS)
* Azure Arc が有効になっている Kubernetes の Azure サブスクリプションにおける共同作成者 RBAC ロール以上のアクセス許可
* Azure Arc が有効になっている Kubernetes クラスターの完全修飾 Azure リソース ID
* Kubernetes クラスターの kubeconfig コンテキスト
* エージェントを監視するには、Kubelet の cAdvisor がいずれかの安全なポートで実行されている必要があります。10250 または安全ではないポート:パフォーマンス メトリックをプルするすべてのノード上の 10255   
* Kubelet cAdvisor ポートを安全なポート 10250 に構成することをお勧めします。
* 監視エージェントでは、監視データを Azure Monitor バックエンドに送信するために、次の送信ポートとドメインが必要となります (プロキシまたはファイアウォールによってブロックされている場合)
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>オンボード

### <a name="using-helm-chart"></a>HELM グラフの使用

### <a name="option-1-using-powershell--script"></a>オプション 1: PowerShell スクリプトの使用

1. オンボード スクリプトのダウンロード

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. 開発用のコンピューターに [PowerShell core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) をインストールし、PowerShell オンボード スクリプトを実行します。

3. Azure にログインする

    ```console
    az login --use-device-code
    ```

4. 自分のクラスターの Azure Arc K8s Cluster ResourceId と Kubernetes クラスターのコンテキストで下のスクリプトを実行します

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>オプション 2:Bash スクリプトの使用

> **ヒント:** このスクリプトでは Bash 4 の機能が使用されているため Bash が最新の状態であることを確認してください。 `bash --version` を使用して、現在のバージョンを確認できます。

1. オンボード スクリプトのダウンロード

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. 自分のクラスターの Azure Arc K8s Cluster ResourceId と Kubernetes クラスターのコンテキストで下のスクリプトを実行します

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>データ収集を構成する

既定では、エージェントでは kube システム名前空間のコンテナーの stdout ログと stderr ログが収集されません。
必要なデータ収集設定でエージェントを構成するには、 https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config を参照してください。

## <a name="configure-scraping-of-prometheus-metrics"></a>Prometheus メトリックのスクレーピングを構成する

Azure Monitor for containers により、Prometheus メトリックがスクレーピングされ、Azure Monitor バックエンドに取り込まれます。
Prometheus スクレイピングを構成する方法については、 https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration の指示を参照してください。

## <a name="user-interface"></a>ユーザー インターフェイス

https://aka.ms/azmon-containers-azurearc に移動してオンボード クラスターを表示します。

## <a name="disable-monitoring"></a>監視を無効にする

何らかの理由により監視を無効にする場合、コンテナーの HELM グラフの Azure Monitor を削除するだけで、コンテナー バックエンドのために監視データを収集し、Azure Monitor に取り込むことを停止できます。

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>次のステップ

* [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)

