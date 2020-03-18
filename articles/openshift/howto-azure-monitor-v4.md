---
title: Azure Red Hat OpenShift 4.3 での Azure Monitor の統合
description: Microsoft Azure Red Hat OpenShift クラスターで Azure Monitor を有効にする方法について説明します。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082848"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Red Hat OpenShift 4.3 での Azure Monitor の統合

> [!IMPORTANT] 
> 現時点では、Azure Red Hat OpenShift 4.3 は、米国東部のプライベート プレビューでのみご利用いただけます。 プライベート プレビューの受け入れは招待によってのみ可能です。 この機能を有効にする前に、必ずサブスクリプションを登録してください。[Azure Red Hat OpenShift プライベート プレビューの登録](https://aka.ms/aro-preview-register)

> [!NOTE]
> プレビュー機能は、セルフサービスかつ現状のまま、現状利用可能な状態で提供され、サービス レベル アグリーメント (SLA) および限定保証からは除外されるものとします。 したがって、これらのフィーチャーはプロダクション環境での使用を目的としたものではありません。

この記事では、オンプレミスまたは任意のクラウド環境でホストされている OpenShift 4.3 クラスター対して、Azure Monitor for containers のプライベート プレビューを有効にする方法について説明します。 同じ手順は、Azure Red Hat OpenShift (ARO) 4.3 クラスターの監視を有効にする場合にも適用されます。  

## <a name="prerequisites"></a>前提条件

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Kubernetes クラスターの kubeconfig へのアクセス
- Azure サブスクリプションへのアクセス。
- Azure Monitor for Containers Helm chart をインストールするための OpenShift 4.3 クラスターへのアクセス
- Azure サブスクリプションでの、共同作成者 RBAC ロール以上のアクセス許可  
- 監視エージェントでは、監視データを Azure Monitor バックエンドに送信するために、次の送信ポートとドメインが必要となります (プロキシまたはファイアウォールによってブロックされている場合)。
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>オンボード

> [!TIP]
> このスクリプトでは Bash 4 の機能が使用されているため Bash が最新の状態であることを確認してください。 `bash --version` を使用して、現在のバージョンを確認できます。

### <a name="download-the-onboarding-script"></a>オンボード スクリプトのダウンロード

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Kubernetes クラスターの azureSubscriptionId、ワークスペース リージョン、clusterName、コンテキストを使用して、次のスクリプトを実行します。

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

次に例を示します。

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>データ収集を構成する

既定では、監視エージェントは、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの {stdout; stderr} コンテナー ログを収集します。  特定の 1 つまたは複数の名前空間に固有のコンテナー ログ コレクションを構成する場合は、[Container Insights エージェントの構成](../azure-monitor/insights/container-insights-agent-config.md)に関するページを参照してください。 ここでは、構成マップを使用して、必要なデータ収集を設定した状態で監視エージェントを構成できます。

## <a name="configure-scraping-of-prometheus-metrics"></a>Prometheus メトリックのスクレーピングを構成する

Azure Monitor for containers により、Prometheus メトリックがスクレーピングされ、Azure Monitor バックエンドに取り込まれます。 Prometheus のスクレーピングを構成する手順については、「[Container Insights Prometheus の構成](../azure-monitor/insights/container-insights-prometheus-integration.md)」に関するページを参照してください。

オンボードが正常に完了したら、[[ハイブリッド監視]](https://aka.ms/azmon-containers-hybrid) に移動し、[環境] で **[すべて]** を選択して、新しくオンボードされた OpenShift v4 クラスターを表示します。

## <a name="disable-monitoring"></a>監視を無効にする

監視を無効にする場合は、次のコマンドを使用して Azure Monitor for Containers Helm chart を削除して、Azure Monitor for containers のバックエンドへの監視データの収集と取り込みを停止します。

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>監視の更新

[オンボード](#onboarding) セクションの説明に従って、同じパラメーターを使用してオンボード スクリプトを再度実行し、最新の Helm chart へと更新します。

## <a name="after-successful-onboarding"></a>オンボードが正常に完了した後

[[ハイブリッド監視]](https://aka.ms/azmon-containers-hybrid) に移動すると、 **[監視対象クラスター]** タブに、新しく有効になった OpenShift/ARO v4 クラスターが正常性状態とともに表示されます。ここでは、 **[クラスター]** 列をクリックすることで、メトリック、インベントリ、ログなどのより深い分析情報を得ることができます。

## <a name="supported-features"></a>サポートされている機能

サポートされている機能の詳細については、[Container Insights の概要](../azure-monitor/insights/container-insights-overview.md)に関するページを参照してください。

フィードバックや質問がありましたら、askcoin@microsoft.com までお問い合わせください。

## <a name="next-steps"></a>次のステップ

監視の詳細については、以下に関するページを参照してください。
- [Container Insights の概要](../azure-monitor/insights/container-insights-overview.md)

- [ログ クエリの概要](../azure-monitor/log-query/log-query-overview.md)
