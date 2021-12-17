---
title: App Service、Functions、および Logic Apps 用に Azure Arc セットアップする
description: ご使用の Azure Arc 対応 Kubernetes クラスターに対して、App Service アプリ、Functions Apps、Logic Apps を有効にする方法を学習します。
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: a330d68ed556a60261ca91e6bfb32fdddf52dc14
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435588"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>Azure Arc 対応の Kubernetes クラスターを設定して、App Service、Functions、Logic Apps を実行します (プレビュー)

[Azure Arc 対応 Kubernetes クラスター](../azure-arc/kubernetes/overview.md)がある場合、それを使用して [App Service 対応のカスタムの場所](overview-arc-integration.md)を作成し、Web アプリ、Functions Apps、Logic Apps をそれにデプロイします。

Azure Arc 対応 Kubernetes を使用すると、オンプレミスまたはクラウドの Kubernetes クラスターを Azure の App Service、Functions、および Logic Apps に表示できるようになります。 アプリを作成し、別の Azure リージョンのようにアプリにデプロイできます。

## <a name="prerequisites"></a>前提条件

Azure アカウントを持っていない場合は、無料アカウントを[新規登録](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)してください。

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>Azure CLI の拡張機能を追加する

[Azure Cloud Shell](../cloud-shell/quickstart.md) で Bash 環境を起動します。

[![新しいウィンドウで Cloud Shell を起動する](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

これらの CLI コマンドはまだコア CLI セットに含まれていないため、次のコマンドを使用して追加します。

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```

## <a name="create-a-connected-cluster"></a>接続されているクラスターを作成する

> [!NOTE]
> このチュートリアルでは、[Azure Kubernetes Service (AKS)](../aks/index.yml) を使用して、環境をゼロから設定するための具体的な手順を説明します。 ただし、運用ワークロードの場合、Azure で既に管理されているため、AKS クラスターで Azure Arc を有効にする必要がない可能性があります。 以下の手順は、このサービスを理解するのに役立ちますが、運用環境のデプロイでは、規範的なものではなく例示と考えてください。 Azure Arc 対応 Kubernetes クラスターを作成する一般的な手順については、「[クイックスタート: 既存の Kubernetes クラスターを Azure Arc に接続する](../azure-arc/kubernetes/quickstart-connect-cluster.md)」を参照してください。

1. パブリック IP アドレスを使用して、Azure Kubernetes Service でクラスターを作成します。 `<group-name>` は、使用するリソース グループ名に置き換えてください。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    ```

    ---
    
2. [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) ファイルを取得し、クラスターへの接続をテストします。 既定では、kubeconfig ファイルは `~/.kube/config` に保存されます。

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. Azure Arc リソースを含むリソース グループを作成します。 `<group-name>` は、使用するリソース グループ名に置き換えてください。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. 作成したクラスターを Azure Arc に接続します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. 次のコマンドを使用して、接続を検証します。 `provisioningState` プロパティは `Succeeded` のように表示されるはずです。 そうではない場合、少し時間をおいてから、もう一度コマンドを実行してください。

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

Azure Arc で App Service を実行するために [Log Analytic ワークスペース](../azure-monitor/logs/quick-create-workspace.md)は必要ありませんが、開発者はこれを使用して、Azure Arc 対応 Kubernetes クラスターで実行されているアプリのアプリケーション ログを取得できます。 

1. わかりやすくするために、この時点でワークスペースを作成します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. 次のコマンドを実行して、既存の Log Analytics ワークスペース用の、エンコードされたワークスペース ID と共有キーを取得します。 これらは、次のステップで必要になります。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64 -w0) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEnc=$(printf %s $logAnalyticsKey | base64 -w0) # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>App Service 拡張機能をインストールする

1. [App Service 拡張機能](overview-arc-integration.md)の目的の名前、リソースをプロビジョニングするクラスター名前空間、App Service Kubernetes 環境の名前について、次の環境変数を設定します。 App Service Kubernetes 環境で作成されたアプリのドメイン名の一部になるため、`<kube-environment-name>` に一意の名前を選択します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. Log Analytics を有効にして、Azure Arc に接続されたクラスターに App Service 拡張機能をインストールします。 ここでも、Log Analytics は必要ありませんが、後で拡張機能に追加することはできないため、この時点で追加するほうが簡単です。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > Log Analytics を統合せずに拡張機能をインストールするには、コマンドから最後の 3 つの `--configuration-settings` パラメーターを削除します。
    >

    コマンド実行時のさまざまな `--configuration-settings` パラメーターについて、次の表で説明します。
    
    | パラメーター | Description |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | 作成されるカスタムの場所に対して作成されるサービス アカウント。 この値は `default` に設定することをお勧めします。 |
    | `appsNamespace` | アプリ定義とポッドをプロビジョニングする名前空間。 拡張機能リリースの名前空間と一致する **必要があります**。 |
    | `clusterName` | この拡張機能に対して作成される App Service Kubernetes 環境の名前。 |
    | `keda.enabled` | [KEDA](https://keda.sh/) を Kubernetes クラスターにインストールする必要があるかどうか。 `true` または `false` を指定できます。 |
    | `buildService.storageClassName` | ビルド成果物を格納するビルド サービスの[ストレージ クラスの名前](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class)。 `default` のような値は、[既定としてマークされているクラス](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)ではなく、`default` という名前のクラスを指定します。  既定は AKS と AKS HCI の有効なストレージ クラスですが、他のディストリビューションやプラットフォームでは有効でない場合があります。 |
    | `buildService.storageAccessMode` | 上記で指定されたストレージ クラスで使用する[アクセス モード](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。 `ReadWriteOnce` または `ReadWriteMany` を指定できます。 |
    | `customConfigMap` | App Service Kubernetes 環境によって設定される構成マップの名前。 現在、この値は `<namespace>/kube-environment-config` である必要があります。`<namespace>` は、上記の `appsNamespace` の値に置き換えてください。 |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Azure Kubernetes Service クラスターが存在するリソース グループの名前。 基盤となるクラスターが Azure Kubernetes Service の場合にのみ、有効かつ必須です。  |
    | `logProcessor.appLogs.destination` | 省略可能。 `log-analytics` または `none` を選びます。none を選ぶとプラットフォーム ログは無効になります。 |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | `logProcessor.appLogs.destination` が `log-analytics` に設定されている場合にのみ、必須です。 base64 でエンコードされた Log Analytics ワークスペース ID。 このパラメーターは、保護された設定として構成する必要があります。 |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | `logProcessor.appLogs.destination` が `log-analytics` に設定されている場合にのみ、必須です。 base64 でエンコードされた Log Analytics ワークスペース共有キー。 このパラメーターは、保護された設定として構成する必要があります。 |
    | | |
        
3. 後で使用できるように、App Service 拡張機能の `id` プロパティを保存します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. 拡張機能が完全にインストールされるまで待ってから、処理を進めてください。 次のコマンドを実行して、この処理が完了するまでターミナル セッションを待機することができます。

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

`kubectl` を使用して、Kubernetes クラスターで作成されたポッドを確認できます。

```bash
kubectl get pods -n $namespace
```

これらのポッドと、システム内でのそれらの役割の詳細については、[App Service 拡張機能によって作成されたポッド](overview-arc-integration.md#pods-created-by-the-app-service-extension)を参照してください。

## <a name="create-a-custom-location"></a>カスタムの場所を作成する

Azure の[カスタムの場所](../azure-arc/kubernetes/custom-locations.md)は、App Service Kubernetes 環境を割り当てるために使用されます。

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. カスタムの場所の目的の名前と、Azure Arc 接続クラスターの ID に次の環境変数を設定します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. カスタムの場所を作成する:

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. 次のコマンドを使用して、カスタムの場所が正常に作成されたことを確認します。 出力で、`provisioningState` プロパティは `Succeeded` のように表示されるはずです。 そうではない場合、少し時間をおいてからもう一度実行してください。

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. 次の手順用にカスタムの場所の ID を保存します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>App Service Kubernetes 環境を作成する

カスタムの場所にアプリを作成するには、[App Service Kubernetes 環境](overview-arc-integration.md#app-service-kubernetes-environment)が必要です。

1. App Service Kubernetes 環境を作成する:
    
    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `      
    ```

    ---
    
2. 次のコマンドを使用して、App Service Kubernetes 環境が正常に作成されたことを確認します。 出力で、`provisioningState` プロパティは `Succeeded` のように表示されるはずです。 そうではない場合、少し時間をおいてからもう一度実行してください。

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>次の手順

- [クイック スタート: Azure Arc で Web アプリを作成する](quickstart-arc.md)
- [Azure Arc で初めての関数を作成する](../azure-functions/create-first-function-arc-cli.md)
- [Azure Arc で初めてのロジック アプリを作成する](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
