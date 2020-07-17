---
title: Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する | Microsoft Docs
description: この記事では、Azure Red Hat OpenShift バージョン 4 以降でホストされている Azure Monitor を使用して Kubernetes クラスターの監視を構成する方法を説明します。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195750"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する

Azure Monitor for containers は、Azure Kubernetes Service (AKS) と AKS エンジン クラスター用の監視エクスペリエンスを提供するものです。 この記事では、[Azure Red Hat OpenShift](../../openshift/intro-openshift.md) バージョン 4.x でホストされている Kubernetes クラスターの監視を有効にし、Azure と同等の監視エクスペリエンスを実現する方法について説明します。

>[!NOTE]
>現時点では、Azure Red Hat OpenShift のサポートはパブリック プレビューの機能です。
>

次のサポートされている方法を使用して、1 つ以上の既存の Azure Red Hat OpenShift v4.x のデプロイに対して Azure Monitor for containers を有効にできます。

- 既存のクラスターの場合は、用意されている bash スクリプトを使用し、[Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) で実行します。

## <a name="supported-and-unsupported-features"></a>サポートされている機能とサポートされていない機能

Azure Monitor for containers では、次の機能を除き、[概要](container-insights-overview.md)記事で説明されている通り Azure Red Hat OpenShift v4.x の監視をサポートしています。

- ライブ データ (プレビュー)
- クラスターノードとポッドから[メトリックを収集](container-insights-update-metrics.md)し、Azure Monitor メトリック データベースに格納する

## <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.0.72 以降

- [Helm 3](https://helm.sh/docs/intro/install/) CLI ツール

- [Bash バージョン 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) コマンドライン ツール

- Azure Monitor for containers の機能を有効にしてアクセスするには、少なくとも、Azure サブスクリプションの Azure *共同作成者* ロールのメンバー、および Azure Monitor for containers で構成されている Log Analytics ワークスペースの [*Log Analytics 共同作成者*](../platform/manage-access.md#manage-access-using-azure-permissions)のメンバーである必要があります。

- 監視データを表示するには、Azure Monitor for containers で構成されている Log Analytics ワークスペースの [*Log Analytics 閲覧者*](../platform/manage-access.md#manage-access-using-azure-permissions) ロールのアクセス権限を持つメンバーであることを確認します。

## <a name="enable-for-an-existing-cluster"></a>既存のクラスターでの有効化

次の手順を実行して、用意されている bash スクリプトを使用して Azure にデプロイされた Azure Red Hat OpenShift バージョン 4 以降のクラスターの監視を有効にします。

1. Azure へのサインイン

    ```azurecli
    az login
    ```

2. 次のコマンドを使用し、監視アドオンを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. クラスターの **kube-context** を特定するには、クラスターに対する `oc login` が正常に完了した後、コマンド `kubectl config current-context` を実行して値をコピーします。

### <a name="integrate-with-an-existing-workspace"></a>既存のワークスペースと統合する

次の手順を実行して、前にダウンロードした bash スクリプトを使用してクラスターの監視を有効にします。 既存の Log Analytics ワークスペースと統合するには、次の手順を実行して、最初に `workspaceResourceId` パラメーターに必要な Log Analytics ワークスペースの完全なリソース ID を特定し、コマンドを実行して、指定されたワークスペースに対して監視アドオンを有効にします。 指定するワークスペースがない場合は、手順 5 に進み、スクリプトを使用して新しいワークスペースの作成を自動実行します。

1. 次のコマンドを使用して、アクセス権のあるすべてのサブスクリプションを一覧表示します。

    ```azurecli
    az account list --all -o table
    ```

    出力は次のようになります。

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    **SubscriptionId** の値をコピーします。

2. 次のコマンドを使用して、Log Analytics ワークスペースをホストしているサブスクリプションに切り替えます。

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 次の例では、既定の JSON 形式で、サブスクリプション内のワークスペースの一覧が表示されます。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    出力でワークスペース名を探し、その Log Analytics ワークスペースについてフィールド **ID** にある完全なリソース ID をコピーします。

4. 次のコマンドを実行して、監視を有効にし、`workspaceResourceId` パラメーターの値を置き換えます。 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    例:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="integrate-with-default-workspace"></a>既定のワークスペースと統合する

次の手順では、ダウンロードした bash スクリプトを使用して、Azure Red Hat OpenShift v4.x クラスターの監視を有効にします。 この例では、ワークスペースを事前に作成したり、既存のワークスペースを指定したりする必要はありません。 このコマンドでは、リージョンにワークスペースがまだ存在しない場合、クラスター サブスクリプションの既定のリソース グループに既定のワークスペースが作成されるので、プロセスが簡単になります。 作成される既定のワークスペースは、*DefaultWorkspace-\<GUID>-\<Region>* のような形式になります。  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="from-the-azure-portal"></a>Azure portal から

Azure Monitor for containers のマルチクラスター ビューでは、 **[監視対象外クラスター]** タブでは、監視が有効になっていない Azure Red Hat OpenShift クラスターが強調表示されます。クラスターの横にある **[有効にする]** オプションを選択しても、ポータルからの監視のオンボードは開始されません。 この記事の前の手順に従って監視を手動で有効にするように、この記事にリダイレクトされます。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal メニュー上または **[ホーム]** ページから、[Azure Monitor] を選択します。 **[分析情報]** セクションで **[コンテナー]** を選択します。

3. **[モニター - コンテナー]** ページで、 **[Non-monitored clusters] (監視対象外のクラスター)** を選択します。

4. 監視対象外のクラスターの一覧でクラスターを検索し、 **[有効にする]** をクリックします。 一覧の結果を確認するには、 **[クラスターの種類]** 列から **ARO** を探します。 **[有効にする]** をクリックすると、この記事にリダイレクトされます。

## <a name="next-steps"></a>次のステップ

- RedHat OpenShift バージョン 4.x クラスターと実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたうえで、Azure Monitor for containers を[使用する方法](container-insights-analyze.md)について学習します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの stdout および stderr コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、[Container Insights エージェント構成](container-insights-agent-config.md)に関するページを参照し、ConfigMap 構成ファイルに必要なデータ収集設定を構成してください。

- クラスターから Prometheus メトリックをスクレーピングして分析するには、[Prometheus メトリックのスクレーピングの構成](container-insights-prometheus-integration.md)に関するページを参照してください。

- Azure Monitor for containers でクラスターの監視を停止する方法については、「[お使いの Azure Red Hat OpenShift クラスターの監視を停止する方法](container-insights-optout-openshift.md)」を参照してください。
