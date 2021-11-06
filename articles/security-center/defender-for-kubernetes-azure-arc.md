---
title: Microsoft Defender for Kubernetes を使用してハイブリッドおよびマルチクラウドの Kubernetes デプロイを保護する
description: オンプレミスおよびマルチクラウド Kubernetes クラスターで Microsoft Defender for Kubernetes を使用する
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 5a0e5fb157d978c84eb8315b77f9474a51cdf418
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422503"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>オンプレミスおよびマルチクラウド環境で実行されている Azure Arc 対応 Kubernetes クラスターを防御する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

**Microsoft Defender for Kubernetes** クラスター拡張機能を使用すると、クラウド クラスターに対して提供されるのと同じ脅威検出機能を使用して、オンプレミスクラスター Azure Kubernetes Serviceできます。 クラスターで [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) を有効にし、このページで説明されているように拡張機能をデプロイします。 

また、この拡張機能を使用して、(マネージド Kubernetes サービス上ではなく) 他のクラウド プロバイダー上の Kubernetes クラスターを保護することもできます。

> [!TIP]
> インストール プロセスに役立つサンプル ファイルをいくつか [GitHub のインストール例](https://aka.ms/kubernetes-extension-installation-examples)に置いておきました。

## <a name="availability"></a>可用性

| 側面 | 詳細 |
|--------|---------|
| リリース状態 | **プレビュー**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| 必要なロールとアクセス許可 | [セキュリティ管理者](../role-based-access-control/built-in-roles.md#security-admin)はアラートを無視できます<br>[セキュリティ閲覧者](../role-based-access-control/built-in-roles.md#security-reader)は、結果を表示できます |
| 価格 | 無料 (プレビュー期間中) |
| サポートされている Kubernetes ディストリビューション | [Azure Stack HCI の Azure Kubernetes Service](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS エンジン](https://github.com/Azure/aks-engine)<br> [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (バージョン 4.6 以降)<br> [VMware Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/) |
| 制限事項 | Azure Arc対応 Kubernetes と Defender 拡張機能では、Google Kubernetes Engine や Elastic Kubernetes Service のようなマネージド Kubernetes オファリングはサポートされていません。 [Defender for Cloud は、Azure Kubernetes Service (AKS)](defender-for-kubernetes-introduction.md) でネイティブに使用できます。クラスターを Azure Arc に接続する必要Azure Arc。 |
| 環境とリージョン | この拡張機能の可用性は、有効な [Kubernetes のAzure Arcと同じです](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>アーキテクチャの概要

AKS 以外のすべての Kubernetes クラスターの場合は、クラスターをクラスターに接続Azure Arc。接続すると、Microsoft Defender for Kubernetes をクラスター拡張機能 [Azure Arc有効な Kubernetes](../azure-arc/kubernetes/overview.md) リソースに [デプロイできます](../azure-arc/kubernetes/extensions.md)。

拡張機能コンポーネントは、クラスター内のすべてのコントロール プレーン ノードから Kubernetes 監査ログ データを収集し、クラウド内の Microsoft Defender for Kubernetes バックエンドに送信して詳細な分析を行います。 拡張機能はデータ パイプラインとして使用される Log Analytics ワークスペースに登録されますが、監査ログ データは Log Analytics ワークスペースに格納されません。

次の図は、Microsoft Defender for Kubernetes と、Azure Arc対応 Kubernetes クラスター間の相互作用を示しています。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Microsoft Defender for Kubernetes と、Azure Arc対応 Kubernetes クラスター間の相互作用を示すアーキテクチャの大きな図。" lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>前提条件

拡張機能を展開する前に、次のことを確認してください。
- [Kubernetes クラスターを Azure Arc に接続する](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- [汎用クラスター拡張機能のドキュメント](../azure-arc/kubernetes/extensions.md#prerequisites)に記載されている前提条件を満たしている。
- 送信アクセス用に、次のエンドポイントで **ポート 443** を構成する。
    - Azure Government クラウド上のクラスターの場合:
        - *.ods.opinsights.azure.us
        - *.oms.opinsights.azure.us
        - :::no-loc text="login.microsoftonline.us":::
    - 他の Azure クラウド デプロイ上のクラスターの場合:
        - *.ods.opinsights.azure.com
        - *.oms.opinsights.azure.com
        - :::no-loc text="login.microsoftonline.com":::

## <a name="deploy-the-defender-extension"></a>Defender 拡張機能をデプロイする

さまざまな方法を使用して Defender 拡張機能をデプロイできます。 詳細な手順については、該当するタブを選択してください。

### <a name="azure-portal"></a>[**Azure portal**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-defender-for-cloud-recommendation"></a>Defender for Cloud の推奨事項の修正ボタンを使用する

Microsoft Defender for Cloud の専用の推奨事項では、次の情報が提供されます。

- Defender for Kubernetes 拡張機能がデプロイされているご自分のクラスターの **可視性**
- 拡張機能がデプロイされていないそれらのクラスターにそれをデプロイするための **[修正]** ボタン

1. Microsoft Defender for Cloud の推奨事項ページで、[セキュリティ強化のセキュリティを有効 **にする] コントロールを** 開きます。

1. フィルターを使用して、 という名前の推奨事項をAzure Arc **有効な Kubernetes** クラスターに Defender の拡張機能がインストールされている必要があります。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Defender 拡張機能をデプロイするための Microsoft Defender for Cloud の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > アクション 列の **修正** アイコンに注目してください

1. 拡張機能を選択すると、正常および異常なリソース (クラスターで拡張機能がデプロイされているものとされていないもの) の詳細が表示されます。

1. 異常なリソースの一覧からクラスターを選択し、 **[修復]** を選択して、修復オプションを含むペインを開きます。

1. 関連する Log Analytics ワークスペースを選択し、 **[x 個のリソースの修復]** を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Defender for Cloud の修正Azure Arcを使用して、Defender 拡張機能をデプロイします。":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-defender-extension"></a>Defender Azure CLIを使用してデプロイする

1. Azure へのログイン:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > ``<your-subscription-id>`` には、ご自分のクラスターを Azure Arc に接続するときに使用したものと同じサブスクリプション ID を必ず使用してください。

1. 次のコマンドを実行して、ご自分の Azure Arc 対応 Kubernetes クラスターに拡張機能をデプロイします。

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Defender 拡張機能の種類でサポートされている構成設定の説明を次に示します。

    | プロパティ | 説明 |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **オプション**。 ご自分の Log Analytics ワークスペースの完全なリソース ID。<br>指定しない場合、リージョンの既定のワークスペースが使用されます。<br><br>完全なリソース ID を入手するには、次のコマンドを実行して、お使いのサブスクリプション内にあるワークスペースのリストを既定の JSON 形式で表示します。<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Log Analytics ワークスペースのリソース ID の構文は次のとおりです。<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}。 <br>[Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)の詳細をご確認ください |
    | auditLogPath |**オプション**。 監査ログ ファイルへの完全パス。<br>指定しない場合、既定のパス ``/var/log/kube-apiserver/audit.log`` が使用されます。<br>AKS Engine の場合、標準のパスは ``/var/log/kubeaudit/audit.log`` です |

    以下のコマンドは、すべての省略可能なフィールドの使用例を示しています。

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-defender-extension"></a>Defender Azure Resource Managerを使用してデプロイする

Azure Resource Managerを使用して Defender 拡張機能をデプロイするには、サブスクリプションに Log Analytics ワークスペースが必要です。 [Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)の詳細をご確認ください。

Defender for Cloud の **インストール例から、azure-defender-extension-arm-template.json** Resource Manager テンプレートを [使用できます](https://aka.ms/kubernetes-extension-installation-examples)。

> [!TIP]
> Resource Manager テンプレートを初めて使用する場合、こちらから開始してください: 「[Azure Resource Manager テンプレートとは](../azure-resource-manager/templates/overview.md)」

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-defender-extension"></a>REST APIを使用して Defender 拡張機能をデプロイする 

Defender 拡張機能REST APIを使用するには、サブスクリプションに Log Analytics ワークスペースが必要です。 [Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)の詳細をご確認ください。

> [!TIP]
> API を使用して Defender 拡張機能をデプロイする最も簡単な方法は、Defender for Cloud のインストール例から提供されている **Postman Collection JSON** の例 [を使用する方法です](https://aka.ms/kubernetes-extension-installation-examples)。
- Postman Collection JSON を変更するか、REST API を使用して拡張機能を手動でデプロイするには、次の PUT コマンドを実行します。

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    各値の説明:

    | 名前            | /   | 必須 | Type   | 説明                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | サブスクリプション ID | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースのサブスクリプション ID |
    | リソース グループ  | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースを含むリソース グループの名前 |
    | クラスター名    | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースの名前  |


    **認証** のために、(他の Azure API と同様に) 実際のヘッダーには、ベアラー トークンが必要です。 ベアラー トークンを取得するには、次のコマンドを実行します。

    `az account get-access-token --subscription <your-subscription-id>` 実際のメッセージの本文には、次の構造を使用します。
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    プロパティの説明を以下に示します。

    | プロパティ | 説明 | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | Log Analytics リソースのワークスペース ID |
    | logAnalytics.key         | Log Analytics リソースのキー | 
    | auditLogPath             | **オプション**。 監査ログ ファイルへの完全パス。 既定値は ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>デプロイを検証する

クラスターに Defender 拡張機能がインストールされていることを確認するには、次のいずれかのタブの手順に従います。

### <a name="azure-portal---defender-for-cloud"></a>[**Azure portal-Defender for Cloud**](#tab/k8s-verify-asc)

### <a name="use-defender-for-cloud-recommendation-to-verify-the-status-of-your-extension"></a>クラウドの推奨事項として Defender を使用して拡張機能の状態を確認する

1. Microsoft Defender for Cloud の 推奨事項 ページで、  **Azure Defender セキュリティ制御を有効にする** を開きます。

1. **[Azure Arc-enabled Kubernetes clusters should have Azure Defender's extension installed]\(Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある\)** という推奨事項を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Defender 拡張機能をデプロイするための Microsoft Defender for Cloud の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. 拡張機能をデプロイしたクラスターが **[正常]** と表示されていることを確認します。


### <a name="azure-portal---azure-arc"></a>[**Azure portal - Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Azure Arc のページを使用して拡張機能の状態を確認する

1. Azure portal から **Azure Arc** を開きます。
1. インフラストラクチャの一覧から **[Kubernetes clusters]\(Kubernetes クラスター\)** を選択し、特定のクラスターを選択します。
1. 拡張機能のページを開きます。 クラスターの拡張機能が一覧表示されます。 Defender 拡張機能が正しくインストールされているかどうかを確認するには、 **[インストールの状態]** 列を確認します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Kubernetes クラスターにインストールされているすべての拡張機能の状態を確認するための Azure Arc ページ。" lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. 詳細については、拡張機能を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Kubernetes クラスターの Azure Arc 拡張機能の完全な詳細。":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Azure CLI を使用して拡張機能がデプロイされていることを確認する

1. Azure CLI で、次のコマンドを実行します。

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. 応答の中から "extensionType": "microsoft.azuredefender.kubernetes" と "installState": "Installed" を探します。

    > [!NOTE]
    > 最初の数分間は、"installState": "Pending" と表示されることがあります。
    
1. 状態が **Installed** と表示されている場合は、`kubeconfig` ファイルが目的のクラスターを指している状態で、お使いのマシン上で次のコマンドを実行して、"azuredefender-XXXXX" という名前のポッドの状態が "Running" であることを確認します。
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>REST API を使用して拡張機能がデプロイされていることを確認する

正常にデプロイされていることを確認する、または拡張機能の状態をいつでも検証するには:

1. 次の GET コマンドを実行します。

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. 応答の中で "extensionType": "microsoft.azuredefender.kubernetes" が "installState": "Installed" になっていることを確認します。

    > [!TIP]
    > 最初の数分間は、"installState": "Pending" と表示されることがあります。

1. 状態が **Installed** と表示されている場合は、`kubeconfig` ファイルが目的のクラスターを指している状態で、お使いのマシン上で次のコマンドを実行して、"azuredefender-XXXXX" という名前のポッドの状態が "Running" であることを確認します。

    ```console
    kubectl get pods -n azuredefender
    ```

---

## <a name="simulate-security-alerts-from-microsoft-defender-for-kubernetes"></a>Microsoft Defender for Kubernetes からのセキュリティアラートをシミュレートする

サポートされているアラートの完全な一覧については、「 [Microsoft Defender For Cloud のすべてのセキュリティアラートのリファレンス表](alerts-reference.md#alerts-k8scluster)」を参照してください。

1. セキュリティの警告をシミュレートするには、次のコマンドを実行します。

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    予期される応答は "リソースが見つかりませんでした" です。

    このアクティビティは、30分以内に Defender によって検出され、セキュリティアラートがトリガーされます。

1. Azure portal で、[Microsoft Defender for Cloud のセキュリティ警告] ページを開き、関連するリソースでアラートを探します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Microsoft Defender for Kubernetes からのサンプルアラート。" lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-defender-extension"></a>Defender 拡張機能を削除しています

以下のタブで説明するように、Azure portal、Azure CLI、または REST API を使用して拡張機能を削除できます。

### <a name="azure-portal---arc"></a>[**Azure portal - Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Azure portal を使用して拡張機能を削除する

1. Azure portal から Azure Arc を開きます。
1. インフラストラクチャの一覧から **[Kubernetes clusters]\(Kubernetes クラスター\)** を選択し、特定のクラスターを選択します。
1. 拡張機能のページを開きます。 クラスターの拡張機能が一覧表示されます。
1. クラスターを選択し、 **[アンインストール]** を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Arc 対応 Kubernetes クラスターからの拡張機能の削除。" lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-defender-extension"></a>Azure CLI を使用して Defender 拡張機能を削除する

1. 次のコマンドを使用して、Microsoft Defender for Kubernetes Arc 拡張機能を削除します。

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    拡張機能の削除には数分かかる場合があります。 待ってから、成功したことの確認を試みることをお勧めします。

1. 拡張機能が正常に削除されたことを確認するには、次のコマンドを実行します。

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    拡張機能リソースが Azure Resource Manager から削除されるまでの遅延はありません。 その後、`kubeconfig` ファイルが目的のクラスターを指している状態で、次のコマンドを実行して、"azuredefender-XXXXX" という名前のポッドがないことを確認します。 

    ```console
    kubectl get pods -n azuredefender
    ```

    ポッドが削除されるまでに数分かかる場合があります。

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-defender-extension"></a>REST API を使用して Defender 拡張機能を削除する 

REST API を使用して拡張機能を削除するには、次の DELETE コマンドを実行します。

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| 名前            | /   | 必須 | Type   | 説明                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| サブスクリプション ID | path | True     | string | ご自分の Azure Arc 対応 Kubernetes クラスターのサブスクリプション ID |
| リソース グループ  | path | True     | string | ご自分の Azure Arc 対応 Kubernetes クラスターのリソース グループ  |
| クラスター名    | path | True     | string | ご自分の Azure Arc 対応 Kubernetes クラスターの名前            |

**認証** のために、(他の Azure API と同様に) 実際のヘッダーには、ベアラー トークンが必要です。 ベアラー トークンを取得するには、次のコマンドを実行します。

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

要求が完了するまでに数分かかる場合があります。

---

## <a name="next-steps"></a>次の手順

このページでは、Azure Arc 対応 Kubernetes クラスター用の Defender 拡張機能をデプロイする方法について説明しました。 Microsoft Defender for Cloud のコンテナーのセキュリティ機能の詳細については、次のページを参照してください。

- [クラウドの Defender でのコンテナーのセキュリティ](container-security.md)
- [Microsoft Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)
- [Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)
