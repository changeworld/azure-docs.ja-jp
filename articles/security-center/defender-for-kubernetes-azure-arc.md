---
title: Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護
description: オンプレミスおよびマルチクラウドの Kubernetes クラスターでの Azure Defender for Kubernetes の使用
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029201"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>オンプレミスおよびマルチクラウド環境で実行されている Azure Arc 対応 Kubernetes クラスターを防御する

**Azure Defender for Kubernetes クラスター拡張機能** を使用すると、Azure Kubernetes Service クラスター用に提供されているのと同じ脅威検出機能を使用して、オンプレミス クラスターを保護できます。 クラスターで [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) を有効にし、このページで説明されているように拡張機能をデプロイします。 

また、この拡張機能を使用して、(マネージド Kubernetes サービス上ではなく) 他のクラウド プロバイダー上の Kubernetes クラスターを保護することもできます。

> [!TIP]
> インストール プロセスに役立つサンプル ファイルをいくつか [GitHub のインストール例](https://aka.ms/kubernetes-extension-installation-examples)に置いておきました。

## <a name="availability"></a>可用性

| 側面 | 詳細 |
|--------|---------|
| リリース状態 | **プレビュー**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| 必要なロールとアクセス許可 | [セキュリティ管理者](../role-based-access-control/built-in-roles.md#security-admin)はアラートを無視できます<br>[セキュリティ閲覧者](../role-based-access-control/built-in-roles.md#security-reader)は、結果を表示できます |
| 価格 | [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) が必要です |
| サポートされている Kubernetes ディストリビューション | [Azure Stack HCI の Azure Kubernetes Service](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS エンジン](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (バージョン 4.6 以降) |
| 制限事項 | Azure Arc 対応 Kubernetes と Azure Defender 拡張機能では、Google Kubernetes Engine や Elastic Kubernetes Service などのマネージド Kubernetes オファリングがサポートされて **いません**。 [Azure Defender は Azure Kubernetes Service (AKS) に対してネイティブに使用でき](defender-for-kubernetes-introduction.md)、クラスターを Azure Arc に接続する必要はありません。 |
| 環境とリージョン | この拡張機能の可用性は、[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) と同じです|

## <a name="architecture-overview"></a>アーキテクチャの概要

AKS 以外のすべての Kubernetes クラスターについては、クラスターを Azure Arc に接続する必要があります。接続したら、Azure Defender for Kubernetes を[クラスター拡張機能](../azure-arc/kubernetes/extensions.md)として [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) リソースにデプロイできます。

拡張機能のコンポーネントは、クラスター内のすべてのコントロール プレーン ノードから Kubernetes の監査ログ データを収集し、それをさらなる分析のためにクラウドの Azure Defender for Kubernetes のバックエンドに送信します。 拡張機能はデータ パイプラインとして使用される Log Analytics ワークスペースに登録されますが、監査ログ データは Log Analytics ワークスペースに格納されません。

この図は、Azure Defender for Kubernetes と Azure Arc 対応 Kubernetes クラスターとの間のやり取りを示しています。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Azure Defender for Kubernetes と Azure Arc 対応 Kubernetes クラスターとの間のやり取りを示したアーキテクチャの概要図。" lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>前提条件

- Azure Defender for Kubernetes が[お使いのサブスクリプションで有効になっている](enable-azure-defender.md)
- ご自分の Kubernetes クラスターが [Azure Arc に接続されている](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- [汎用クラスター拡張機能のドキュメント](../azure-arc/kubernetes/extensions.md#prerequisites)に記載されている前提条件を満たしている。

## <a name="deploy-the-azure-defender-extension"></a>Azure Defender 拡張機能のデプロイ

さまざまな方法を使用して、Azure Defender 拡張機能をデプロイできます。 詳細な手順については、該当するタブを選択してください。

### <a name="azure-portal"></a>[**Azure portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Security Center の推奨事項の [クイック修正] オプションを使用する

Azure Security Center 専用の推奨事項では、次が提供されます。

- Defender for Kubernetes 拡張機能がデプロイされているご自分のクラスターの **可視性**
- 拡張機能がデプロイされていないそれらのクラスターにそれをデプロイするための **"クイック修正" オプション**

1. Azure Security Center の [推奨事項] ページから **[Azure Defender の有効化]** セキュリティ コントロールを開きます。

1. フィルターを使用して、 **[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある]** という推奨事項を見つけます。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイするための Azure Security Center の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > [アクション] 列の [クイック修正] アイコンに注目してください

1. 拡張機能を選択すると、正常および異常なリソース (クラスターで拡張機能がデプロイされているものとされていないもの) の詳細が表示されます。

1. 異常なリソースの一覧からクラスターを選択し、 **[修復]** を選択して、修復オプションを含むペインを開きます。

1. 関連する Log Analytics ワークスペースを選択し、 **[x 個のリソースの修復]** を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Security Center の[クイック修正] オプションを使用して Azure Arc 用の Azure Defender 拡張機能をデプロイする。":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Azure CLI を使用して Azure Defender 拡張機能をデプロイする

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

    Azure Defender 拡張機能の種類でサポートされているすべての構成設定の説明を次に示します。

    | プロパティ | 説明 |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **オプション**。 ご自分の Log Analytics ワークスペースの完全なリソース ID。<br>指定しない場合、リージョンの既定のワークスペースが使用されます。<br><br>完全なリソース ID を入手するには、次のコマンドを実行して、お使いのサブスクリプション内にあるワークスペースのリストを既定の JSON 形式で表示します。<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Log Analytics ワークスペースのリソース ID の構文は次のとおりです。<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}。 <br>[Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)の詳細をご確認ください |
    | auditLogPath |**オプション**。 監査ログ ファイルへの完全パス。<br>指定しない場合、既定のパス ``/var/log/kube-apiserver/audit.log`` が使用されます。<br>AKS Engine の場合、標準のパスは ``/var/log/kubeaudit/audit.log`` です |

    以下のコマンドは、すべての省略可能なフィールドの使用例を示しています。

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Azure Resource Manager を使用して Azure Defender 拡張機能をデプロイする

Azure Resource Manager を使用して Azure Defender 拡張機能をデプロイするには、お使いのサブスクリプションに Log Analytics ワークスペースが必要です。 [Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)の詳細をご確認ください。

Security Center の [インストール例](https://aka.ms/kubernetes-extension-installation-examples)にある Resource Manager テンプレートの **azure-defender-extension-arm-template.json** を使用できます。

> [!TIP]
> Resource Manager テンプレートを初めて使用する場合、こちらから開始してください: 「[Azure Resource Manager テンプレートとは](../azure-resource-manager/templates/overview.md)」

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>REST API を使用して Azure Defender 拡張機能をデプロイする 

REST API を使用して Azure Defender 拡張機能をデプロイするには、お使いのサブスクリプションに Log Analytics ワークスペースが必要です。 [Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)の詳細をご確認ください。

> [!TIP]
> API を使用して Azure Defender 拡張機能をデプロイする最も簡単な方法は、Security Center の [インストール例](https://aka.ms/kubernetes-extension-installation-examples)で提供されている **Postman Collection JSON** の例を使用することです。
- Postman Collection JSON を変更するか、REST API を使用して拡張機能を手動でデプロイするには、次の PUT コマンドを実行します。

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    各値の説明:

    | 名前            | /   | 必須 | 種類   | 説明                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | サブスクリプション ID | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースのサブスクリプション ID |
    | リソース グループ  | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースを含むリソース グループの名前 |
    | クラスター名    | path | True     | string | お使いの Azure Arc 対応 Kubernetes リソースの名前  |


    **認証** のために、(他の Azure API と同様に) 実際のヘッダーには、ベアラー トークンが必要です。 ベアラー トークンを取得するには、次のコマンドを実行します。

    ```az account get-access-token --subscription <your-subscription-id>``` 実際のメッセージの本文には、次の構造を使用します。
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

ご自分のクラスターに Azure Defender 拡張機能がインストールされていることを確認するには、以下のいずれかのタブにある手順に従ってください。

### <a name="azure-portal---security-center"></a>[**Azure portal - Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Security Center の推奨事項を使用して拡張機能の状態を確認する

1. Azure Security Center の [推奨事項] ページから **[Azure Defender の有効化]** セキュリティ コントロールを開きます。

1. **[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある]** という推奨事項を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイするための Azure Security Center の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. 拡張機能をデプロイしたクラスターが **[正常]** と表示されていることを確認します。


### <a name="azure-portal---azure-arc"></a>[**Azure portal - Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Azure Arc のページを使用して拡張機能の状態を確認する

1. Azure portal から **Azure Arc** を開きます。
1. インフラストラクチャの一覧から **[Kubernetes clusters]\(Kubernetes クラスター\)** を選択し、特定のクラスターを選択します。
1. 拡張機能のページを開きます。 クラスターの拡張機能が一覧表示されます。 **[インストール ステータス]** 列をチェックして、Azure Defender 拡張機能が正しくインストールされていることを確認します。

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

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes からのセキュリティ アラートをシミュレートする

サポートされているアラートの完全な一覧については、[Azure Security Center のすべてのセキュリティ アラートのリファレンス テーブル](alerts-reference.md#alerts-akscluster)を参照してください。

1. Azure Defender アラートをシミュレートするには、次のコマンドを実行します。

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    予期される応答は "リソースが見つかりませんでした" です。

    30 分以内に、Azure Defender によって、このアクティビティが検出され、セキュリティ アラートがトリガーされます。

1. Azure portal で Azure Security Center の [セキュリティ アラート] ページを開き、関連するリソースのアラートを探します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Azure Defender for Kubernetes のサンプル アラート。" lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Azure Defender 拡張機能を削除する

以下のタブで説明するように、Azure portal、Azure CLI、または REST API を使用して拡張機能を削除できます。

### <a name="azure-portal---arc"></a>[**Azure portal - Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Azure portal を使用して拡張機能を削除する

1. Azure portal から Azure Arc を開きます。
1. インフラストラクチャの一覧から **[Kubernetes clusters]\(Kubernetes クラスター\)** を選択し、特定のクラスターを選択します。
1. 拡張機能のページを開きます。 クラスターの拡張機能が一覧表示されます。
1. クラスターを選択し、 **[アンインストール]** を選択します。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Arc 対応 Kubernetes クラスターからの拡張機能の削除。" lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Azure CLI を使用して Azure Defender 拡張機能を削除する

1. 次のコマンドを使用して、Azure Defender for Kubernetes Arc 拡張機能を削除します。

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

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>REST API を使用して Azure Defender 拡張機能を削除する 

REST API を使用して拡張機能を削除するには、次の DELETE コマンドを実行します。

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| 名前            | /   | 必須 | 種類   | 説明                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| サブスクリプション ID | path | True     | string | ご自分の Arc 対応 Kubernetes クラスターのサブスクリプション ID |
| リソース グループ  | path | True     | string | ご自分の Arc 対応 Kubernetes クラスターのリソース グループ  |
| クラスター名    | path | True     | string | ご自分の Arc 対応 Kubernetes クラスターの名前            |

**認証** のために、(他の Azure API と同様に) 実際のヘッダーには、ベアラー トークンが必要です。 ベアラー トークンを取得するには、次のコマンドを実行します。

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

要求が完了するまでに数分かかる場合があります。

---

## <a name="next-steps"></a>次のステップ

このページでは、Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイする方法について説明しました。 Azure Defender と Azure Security Center のコンテナー セキュリティ機能の詳細については、次のページを参照してください。

- [Security Center のコンテナーのセキュリティ](container-security.md)
- [Azure Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)
- [Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)
