---
title: Azure Arc 対応 Kubernetes クラスターを監視する
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Monitor を使用して Azure Arc 対応 Kubernetes クラスターのメトリックとログを収集する
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443852"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 対応 Kubernetes クラスター用の Azure Monitor Container Insights

[Azure Monitor Container Insights](container-insights-overview.md) を使用すると、Azure Arc 対応 Kubernetes クラスターの監視エクスペリエンスを充実させることができます。

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>サポートされている構成

- Azure Monitor Container Insights では、「[概要](container-insights-overview.md)」の記事で説明されているように Azure Arc 対応 Kubernetes の監視 (プレビュー) がサポートされます。ただし、ライブ データ (プレビュー) 機能は除きます。 また、ユーザーには、[メトリックを有効にする](container-insights-update-metrics.md)ための[所有者](../../role-based-access-control/built-in-roles.md#owner)権限は必要ありません。
- `Docker`、`Moby`、CRI 互換コンテナー ランタイム (`CRI-O`、`containerd`など)。
- 認証なしの送信プロキシと基本認証を使用した送信プロキシがサポートされます。 現時点では、信頼できる証明書が必要な送信プロキシはサポートされていません。

## <a name="prerequisites"></a>前提条件

- [汎用クラスターの拡張機能のドキュメント](../../azure-arc/kubernetes/extensions.md#prerequisites)に記載されている前提条件を満たしていること。
- Log Analytics ワークスペース: Azure Monitor Container Insights では、[リージョン別の Azure 製品のページ](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に記載されているリージョンで Log Analytics ワークスペースがサポートされます。 [Azure Resource Manager](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md)、または [Azure portal](../logs/quick-create-workspace.md) を使用して独自のワークスペースを作成できます。
- Azure Arc 対応 Kubernetes リソースを含む Azure サブスクリプションで、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール割り当てが必要です。 Log Analytics ワークスペースが別のサブスクリプションに含まれる場合、その Log Analytics ワークスペースで、[Log Analytics 共同作成者](../logs/manage-access.md#manage-access-using-azure-permissions)ロール割り当てが必要です。
- 監視データを表示するために、Log Analytics ワークスペースで、[Log Analytics 閲覧者](../logs/manage-access.md#manage-access-using-azure-permissions)ロール割り当てが必要です。
- 送信アクセスのために、「[Kubernetes クラスターを Azure Arc に接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)」で示されているエンドポイントに加えて、下記に示すものも有効にする必要があります。

    | エンドポイント | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Arc 対応 Kubernetes リソースが Azure US Government 環境にある場合、送信アクセスのために次のエンドポイントを有効にする必要があります。

    | エンドポイント | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- 以前に、クラスター拡張機能のないスクリプトを使用してこのクラスターに Azure Monitor Container Insights をデプロイした場合、[こちら](container-insights-optout-hybrid.md)の指示に従って、この Helm チャートを削除してください。 その後、Azure Monitor Container Insights のクラスター拡張機能インスタンスの作成を続行できます。

    >[!NOTE]
    > Azure Monitor Container Insights のデプロイのスクリプトベース バージョン (プレビュー) は、[クラスター拡張機能](../../azure-arc/kubernetes/extensions.md)形式のデプロイに置き換えられます。 スクリプトを使用して以前デプロイされた Azure Monitor は 2021 年 6 月までしかサポートされないため、できるだけ早い段階でクラスター拡張機能形式のデプロイに移行することをお勧めします。

### <a name="identify-workspace-resource-id"></a>ワークスペースのリソース ID の特定

次のコマンドを実行して、Log Analytics ワークスペースの完全な Azure Resource Manager ID を見つけます。 

1. 次のコマンドを使用して、アクセス権のあるすべてのサブスクリプションを一覧表示します。

    ```azurecli
    az account list --all -o table
    ```

2. 次のコマンドを使用して、Log Analytics ワークスペースをホストしているサブスクリプションに切り替えます。

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 次の例では、既定の JSON 形式で、サブスクリプション内のワークスペースの一覧が表示されます。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    出力で、目的のワークスペース名を見つけます。 その `id` フィールドが、その Log Analytics ワークスペースの Azure Resource Manager ID を表します。

    >[!TIP]
    > この `id` は、Azure portal の Log Analytics ワークスペースの *[概要]* ブレードでも見つけることができます。

## <a name="create-extension-instance-using-azure-cli"></a>Azure CLI を使用して拡張機能インスタンスを作成する

### <a name="option-1---with-default-values"></a>オプション 1 - 既定値を使用

このオプションでは、次の既定値が使用されます。

- クラスターのリージョンに対応する既定の Log Analytics ワークスペースを作成または使用します
- Azure Monitor クラスター拡張機能に対して自動アップグレードが有効化されます

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>オプション 2 - 既存の Azure Log Analytics ワークスペースを使用

"*共同作成者*" またはより制限が少ないロール割り当てがある任意のサブスクリプションで、既存の Azure Log Analytics ワークスペースを使用できます。

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>オプション 3 - 詳細な構成を使用

既定のリソース要求と制限を調整する場合、詳細な構成設定を使用できます。

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

使用可能な構成設定については、[Helm チャートのリソース要求と制限に関するセクション](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml)を参照してください。

### <a name="option-4---on-azure-stack-edge"></a>オプション 4 - Azure Stack Edge

Azure Arc 対応 Kubernetes クラスターが Azure Stack Edge にある場合は、カスタム マウント パス `/home/data/docker` を使用する必要があります。

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> create コマンドでインストールする拡張機能のバージョンを明示的に指定する場合、指定するバージョンが 2.8.2 以上であることを確認してください。

## <a name="create-extension-instance-using-azure-portal"></a>Azure portal を使用して拡張機能インスタンスを作成する

>[!IMPORTANT]
>  Azure Stack Edge 上で実行されている Kubernetes クラスターに Azure Monitor をデプロイする場合、これらのクラスターに対してカスタム マウント パスを設定する必要があるため、Azure portal オプションの代わりに Azure CLI オプションに従う必要があります。    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Azure Arc 対応 Kubernetes リソース ブレードからのオンボード

1. Azure portal で、監視する Arc 対応 Kubernetes クラスターを選択します。

2. リソース ブレードの [監視] セクションの下にある [Insights (プレビュー)] 項目を選択します。

3. オンボード ページで、[Azure Monitor の構成] ボタンを選択します。

4. これで、メトリックとログ データの送信先の [Log Analytics ワークスペース](../logs/quick-create-workspace.md)を選択できます。

5. [構成] ボタンを選択して、Azure Monitor Container Insights クラスター拡張機能をデプロイします。

### <a name="onboarding-from-azure-monitor-blade"></a>Azure Monitor ブレードからのオンボード

1. Azure portal で、[Monitor] ブレードに移動し、[Insights] メニューの [コンテナー] オプションを選択します。

2. [監視対象外のクラスター] タブを選択して、監視を有効にできる Azure Arc 対応 Kubernetes クラスターを表示します。

3. 監視を有効にするクラスターの横にある [有効化] リンクをクリックします。

4. Log Analytics ワークスペースを選択し、[構成] ボタンを選択して続行します。

## <a name="create-extension-instance-using-azure-resource-manager"></a>Azure Resource Manager を使用して拡張機能インスタンスを作成する

1. Azure Resource Manager テンプレートとパラメーターをダウンロードします。

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. arc-k8s-azmon-extension-arm-template-params.json ファイルで、パラメーター値を更新します。Azure パブリック クラウドの場合、workspaceDomain の値として `opinsights.azure.com` を使用する必要があります。

3. テンプレートをデプロイして Azure Monitor Container Insights 拡張機能を作成します。 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>拡張機能インスタンスを削除する

次のコマンドでは、拡張機能インスタンスのみが削除され、Log Analytics ワークスペースは削除されません。 Log Analytics リソース内のデータはそのまま残ります。

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>切断されたクラスター
クラスターが 48 時間よりも長く Azure から切断されると、Azure Resource Graph にはクラスターに関する情報がなくなります。 この結果、Insights ブレードに、クラスターの状態に関する誤った情報が表示される場合があります。

## <a name="next-steps"></a>次のステップ

- Arc 対応 Kubernetes クラスターとそこで実行されるワークロードの正常性とリソース使用率を収集するための監視を有効にしたので、Container insights の[使い方](container-insights-analyze.md)を確認します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの stdout および stderr コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、「[コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する](container-insights-agent-config.md)」を参照して、ConfigMap 構成ファイルに必要なデータ収集設定を構成します。

- クラスターから Prometheus メトリックをスクレイピングして分析するには、「[Azure Monitor for containers で Prometheus メトリックのスクレーピングを構成する](container-insights-prometheus-integration.md)」を参照してください。
