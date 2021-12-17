---
title: コンテナー分析情報を有効にする | Microsoft Docs
description: この記事では、コンテナーのパフォーマンスと特定されたパフォーマンスに関する問題を把握できるように、コンテナー分析情報を有効にして構成する方法について説明します。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2d47ea7f2f2f0dadfd979a42b0b0e9125d4bebde
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455114"
---
# <a name="enable-container-insights"></a>コンテナー分析情報を有効にする

この記事では、Kubernetes 環境にデプロイされ、以下でホストされているワークロードのパフォーマンスを監視することを目的として、コンテナー分析情報を設定するために使用できるオプションの概要について説明します。

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Arc 対応 Kubernetes クラスター](../../azure-arc/kubernetes/overview.md)
   - [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) またはオンプレミス
   - [AKS エンジン](https://github.com/Azure/aks-engine)
   - [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) バージョン 4.x  
   - [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) バージョン 4.x  


次のいずれかのサポートされている方法を使用して、Kubernetes の新しいデプロイまたは 1 つ以上の既存のデプロイに対して、コンテナー分析情報を有効にできます。

- Azure ポータル
- Azure PowerShell
- Azure CLI
- [Terraform と AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

非 AKS Kubernetes クラスターの場合は、監視を有効にする前に、まずクラスターを [Azure Arc](../../azure-arc/kubernetes/overview.md) に接続する必要があります。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

始める前に、次の要件を満たしていることを確認します。

> [!IMPORTANT]
> Log Analytics のコンテナー化された Linux エージェント (replicaset ポッド) を使用すると、クラスター内の Kubelet で保護されたポート (10250) 上のすべての Windows ノードに API 呼び出しを行い、ノードとコンテナーのパフォーマンスに関連するメトリックが収集されます。 Windows ノードとコンテナーのパフォーマンスに関連するメトリックの収集が機能するためには、インバウンドとアウトバウンドの両方で、クラスターの仮想ネットワークの Kubelet で保護されたポート (:10250) が開かれている必要があります。
>
> Windows ノードを持つ Kubernetes クラスターがある場合は、クラスターの仮想ネットワークのインバウンドとアウトバウンドの両方で、Kubelet で保護されたポート (:10250) が開かれていることを確認し、ネットワーク セキュリティ グループとネットワーク ポリシーを構成してください。


- Log Analytics ワークスペースがあります。

   コンテナー分析情報では、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)の一覧で示されているリージョンの Log Analytics ワークスペースがサポートされます。

   新しい AKS クラスターに対する監視を有効にするときにワークスペースを作成すること、またはオンボード エクスペリエンスを使用して AKS クラスター サブスクリプションの既定のリソース グループに既定のワークスペースを作成することができます。 
   
   自分でワークスペースを作成する場合は、次を使用して作成できます。 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure ポータル](../logs/quick-create-workspace.md) 
   
   既定のワークスペースに使用する、サポートされているマッピング ペアの一覧については、[コンテナー分析情報のリージョンのマッピング](container-insights-region-mapping.md)に関するページを参照してください。

- コンテナーの監視を有効にする *Log Analytics 共同作成者ロール* のメンバーです。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../logs/manage-access.md)」を参照してください。

- AKS クラスター リソースに対する ["*所有者*" グループ](../../role-based-access-control/built-in-roles.md#owner)のメンバーです。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- 監視データを表示するには、コンテナー分析情報で構成された Log Analytics ワークスペースの "[*Log Analytics 閲覧者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールを持っている必要があります。

- 既定では、Prometheus のメトリックは収集されません。 メトリックを収集するための[エージェントを構成する](container-insights-prometheus-integration.md)前に、[Prometheus のドキュメント](https://prometheus.io/)を確認して、スクレイピングできるデータおよびサポートされているメソッドを理解しておくことが重要です。
- AKS クラスターは、同じ Azure AD テナント内の別の Azure サブスクリプションの Log Analytics ワークスペースに接続できます。 現時点では、これは Azure portal では実行できませんが、Azure CLI または Resource Manager テンプレートを使用して行うことができます。

## <a name="supported-configurations"></a>サポートされている構成

コンテナー分析情報では、次の構成が正式にサポートされています。

- 環境:Azure Red Hat OpenShift、オンプレミスの Kubernetes、Azure および Azure Stack 上の AKS エンジン。 詳細については、[Azure Stack 上の AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)に関するページを参照してください。
- Kubernetes のバージョンとサポート ポリシーは、[Azure Kubernetes Service (AKS) でサポートされている](../../aks/supported-kubernetes-versions.md)ものと同じです。
- クラスターを [Azure Arc](../../azure-arc/kubernetes/overview.md) に接続し、Azure Arc を介した Container Insights による監視を有効にすることをお勧めします。

> [!IMPORTANT]
> 監視アドオンは、[HTTP プロキシ (プレビュー)](../../aks/http-proxy.md) で構成された AKS クラスターでは現在サポートされていないので注意してください

## <a name="network-firewall-requirements"></a>ネットワーク ファイアウォールの要件

次の表は、コンテナー化されたエージェントがコンテナー分析情報と通信するために必要なプロキシおよびファイアウォールの構成情報をまとめたものです。 エージェントからのすべてのネットワーク トラフィックは、Azure Monitor に送信されます。

|エージェントのリソース|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

次の表は、Azure China 21Vianet のプロキシとファイアウォールの構成情報をまとめたものです。

|エージェントのリソース|Port |説明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | データ インジェスト |
| `*.oms.opinsights.azure.cn` | 443 | OMS のオンボード |
| `dc.services.visualstudio.com` | 443 | Azure パブリック クラウド Application Insights を使用するエージェント テレメトリの場合 |

次の表は、Azure US Government のプロキシとファイアウォールの構成情報をまとめたものです。

|エージェントのリソース|Port |説明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | データ インジェスト |
| `*.oms.opinsights.azure.us` | 443 | OMS のオンボード |
| `dc.services.visualstudio.com` | 443 | Azure パブリック クラウド Application Insights を使用するエージェント テレメトリの場合 |

## <a name="components"></a>コンポーネント

パフォーマンスを監視する能力は、コンテナー分析情報用に特別に開発された、Linux 用のコンテナー化 Log Analytics エージェントに依存します。 この特殊なエージェントは、クラスター内のすべてのノードからパフォーマンスとイベント データを収集します。エージェントは自動的にデプロイされ、デプロイ時に指定した Log Analytics ワークスペースに登録されます。 

このエージェントのバージョンは microsoft/oms:ciprod04202018 以降であり、*mmddyyyy* という形式の日付で表されます。

>[!NOTE]
>一般提供されるようになった Windows Server による AKS のサポートでの、Windows Server ノードが含まれる AKS クラスターでは、ログを収集して Log Analytics に転送するため、個々の Windows Server ノードに、デーモンセット ポッドとしてプレビュー エージェントがインストールされています。 パフォーマンス メトリックの場合は、標準のデプロイの一部としてクラスターに自動的にデプロイされる Linux ノードにより、クラスターのすべての Windows ノードに代わってデータが収集されて、Azure Monitor に転送されます。

エージェントの新しいバージョンがリリースされた場合でも、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされることはありません。 リリースされているバージョンを追跡するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。

> [!NOTE]
> AKS クラスターを既にデプロイしてある場合は、この記事で後ほど示すように、Azure CLI または提供されている Azure Resource Manager テンプレートを使用して、監視を有効にします。 `kubectl` を使用して、エージェントのアップグレード、削除、再デプロイ、デプロイを行うことはできません。
>
> テンプレートはクラスターと同じリソース グループ内に展開する必要があります。

コンテナー分析情報を有効にするには、次の表で説明されている方法のいずれかを使用します。

| デプロイの状態 | Method | 説明 |
|------------------|--------|-------------|
| 新しい Kubernetes クラスター | [Azure CLI を使用して AKS クラスターを作成する](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| | [Terraform を使用して AKS クラスターを作成する](container-insights-enable-new-cluster.md#enable-using-terraform)| オープンソースのツールである Terraform を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| | [Azure Resource Manager テンプレートを使用して OpenShift クラスターを作成する](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 事前構成済みの Azure Resource Manager テンプレートを使用して作成した新しい OpenShift クラスターの監視を有効にできます。 |
| | [Azure CLI を使用して OpenShift クラスターを作成する](/cli/azure/openshift#az_openshift_create) | Azure CLI を使用して新しい OpenShift クラスターをデプロイするときに、監視を有効にすることができます。 |
| 既存の AKS クラスター | [Azure CLI を使用して AKS クラスターの監視を有効にする](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| |[Terraform を使用して AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-using-terraform) | オープンソースのツールである Terraform を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| | [Azure Monitor から AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor のマルチクラスター ページから、既にデプロイされている 1 つまたは複数の AKS クラスターの監視を有効にできます。 |
| | [AKS クラスターから有効にする](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure portal の AKS クラスターから直接監視を有効にできます。 |
| | [Azure Resource Manager テンプレートを使用して AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 事前構成済みの Azure Resource Manager テンプレートを使用して AKS クラスターの監視を有効にできます。 |
| 既存の非 AKS Kubernetes クラスター | [Azure CLI を使用して非 AKS Kubernetes クラスターを有効にする](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-cli)。 | Azure の外部でホストされ、Azure Arc で有効になっている Kubernetes クラスターの監視を有効にできます。これには、Azure CLI を使用したハイブリッド、OpenShift、マルチクラウドが含まれます。 |
| | [Azure Resource Manager テンプレートを使用して非 AKS Kubernetes クラスターを有効にする](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-resource-manager) | 事前構成済みの Azure Resource Manager テンプレートを使用して Arc 対応クラスターの監視を有効にすることができます。 |
| | [Azure Monitor から非 AKS Kubernetes クラスターを有効にする](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-portal) | Azure Monitor のマルチクラスター ページから、既にデプロイされている 1 つ以上の Arc 対応クラスターの監視を有効にすることができます。 |

## <a name="next-steps"></a>次のステップ

監視を有効にしたので、Azure Kubernetes Service (AKS)、Azure Stack、または他の環境でホストされている Kubernetes クラスターのパフォーマンスの分析を開始できます。 コンテナー分析情報を使用する方法については、[Kubernetes クラスターのパフォーマンスの表示](container-insights-analyze.md)に関するページをご覧ください。
