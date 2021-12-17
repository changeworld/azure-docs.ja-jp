---
title: 'クイックスタート: 既存の Kubernetes クラスターを Azure Arc に接続する'
description: このクイックスタートでは、Azure Arc 対応 Kubernetes クラスターを接続する方法について説明します。
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 09/09/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, クラスター
ms.openlocfilehash: 6716ae8b85893b9af4b439ea76eca631bff525bf
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066454"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>クイックスタート: 既存の Kubernetes クラスターを Azure Arc に接続する

このクイックスタートでは、Azure Arc 対応 Kubernetes のメリットおよび既存の Kubernetes クラスターを Azure Arc に接続する方法について説明します。Azure Arc にクラスターを接続することの概念的な見方については、[Azure Arc 対応 Kubernetes エージェントのアーキテクチャに関する記事](./conceptual-agent-architecture.md)をご覧ください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](/cli/azure/install-azure-cli)

* **connectedk8s** Azure CLI 拡張機能バージョン 1.2.0 以降をインストールします。

  ```console
  az extension add --name connectedk8s
  ```

* 稼働している Kubernetes クラスター。 お持ちでない場合は、次のいずれかのオプションを使用してクラスターを作成できます。
    * [Docker 内の Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する
    * [Cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html) を使用したセルフマネージド Kubernetes クラスター
    * OpenShift クラスターを Azure Arc に接続する場合は、`az connectedk8s connect` を実行する前に、クラスターで次のコマンドを 1 回だけ実行する必要があります。

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > クラスターには、オペレーティング システムとアーキテクチャの種類が `linux/amd64` であるノードが少なくとも 1 つ含まれている必要があります。 `linux/arm64` ノードのみが含まれるクラスターはまだサポートされていません。

* `kubeconfig` ファイルと、クラスターを指すコンテキスト。
* Azure Arc 対応 Kubernetes リソースの種類 (`Microsoft.Kubernetes/connectedClusters`) に対する "読み取り" および "書き込み" アクセス許可。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* [Azure PowerShell バージョン 5.9.0 以降](/powershell/azure/install-az-ps)

* **Az.ConnectedKubernetes** PowerShell モジュールをインストールします。

    ```azurepowershell-interactive
    Install-Module -Name Az.ConnectedKubernetes
    ```

    > [!IMPORTANT]
    > **Az.ConnectedKubernetes** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。

* 稼働している Kubernetes クラスター。 お持ちでない場合は、次のいずれかのオプションを使用してクラスターを作成できます。
    * [Docker 内の Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する
    * [Cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html) を使用したセルフマネージド Kubernetes クラスター
    * OpenShift クラスターを Azure Arc に接続する場合は、`New-AzConnectedKubernetes` を実行する前に、クラスターで次のコマンドを 1 回だけ実行する必要があります。

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > クラスターには、オペレーティング システムとアーキテクチャの種類が `linux/amd64` であるノードが少なくとも 1 つ含まれている必要があります。 `linux/arm64` ノードのみが含まれるクラスターはまだサポートされていません。

* `kubeconfig` ファイルと、クラスターを指すコンテキスト。
* Azure Arc 対応 Kubernetes リソースの種類 (`Microsoft.Kubernetes/connectedClusters`) に対する "読み取り" および "書き込み" アクセス許可。

* [Helm 3](https://helm.sh/docs/intro/install) をインストールします。 Helm 3 のバージョンが &lt; 3.7.0 であることを確認します。

---

## <a name="meet-network-requirements"></a>ネットワーク要件を満たす

> [!IMPORTANT]
> Azure Arc エージェントが機能するには、`https://:443` に次の送信 URL が必要です。
> `*.servicebus.windows.net` の場合、ファイアウォールとプロキシで送信アクセスに対して Websockets を有効にする必要があります。

| エンドポイント (DNS) | 説明 |
| ----------------- | ------------- |
| `https://management.azure.com` (Azure Cloud の場合)、`https://management.usgovcloudapi.net` (Azure US Government の場合) | エージェントが Azure に接続し、クラスターを登録するために必要です。 |
| `https://<region>.dp.kubernetesconfiguration.azure.com` (Azure Cloud の場合)、`https://<region>.dp.kubernetesconfiguration.azure.us` (Azure US Government の場合) | エージェントが状態をプッシュして構成情報をフェッチするためのデータ プレーン エンドポイント。 |
| `https://login.microsoftonline.com`、`login.windows.net` (Azure Cloud の場合)、`https://login.microsoftonline.us` (Azure US Government の場合) | Azure Resource Manager トークンをフェッチし、更新するために必要です。 |
| `https://mcr.microsoft.com`, `https://*.data.mcr.microsoft.com` | Azure Arc エージェント用のコンテナー イメージをプルするために必要です。                                                                  |
| `https://gbl.his.arc.azure.com` (Azure Cloud の場合)、`https://gbl.his.arc.azure.us` (Azure US Government の場合) |  システム割り当て管理 ID 証明書をプルするためリージョン エンドポイントを取得するために必要です。 |
| `https://*.his.arc.azure.com` (Azure Cloud の場合)、`https://usgv.his.arc.azure.us` (Azure US Government の場合) |  システム割り当てマネージド ID 証明書をプルするために必須。 |
|`*.servicebus.windows.net`, `guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`, `sts.windows.net` | [クラスター接続](cluster-connect.md)ベース シナリオの場合と、[カスタムの場所](custom-locations.md)ベースのシナリオの場合。 |
|`https://k8connecthelm.azureedge.net` | `az connectedk8s connect` では、Helm 3 を使用して、Kubernetes クラスターに Azure Arc エージェントをデプロイします。 このエンドポイントは、エージェント Helm chart のデプロイを容易にするために、Helm クライアントのダウンロードに必要です。 |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Azure Arc 対応 Kubernetes 用のプロバイダーを登録する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 次のコマンドを入力します。
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

    登録すると、これらの名前空間の `RegistrationState` 状態が `Registered` に変わります。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. 次のコマンドを入力します。
    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。
    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

    登録すると、これらの名前空間の `RegistrationState` 状態が `Registered` に変わります。
---

## <a name="2-create-a-resource-group"></a>2.リソース グループを作成する

次のコマンドを実行します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name AzureArcTest --location EastUS --output table
```

Output:
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

出力:
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 既存の Kubernetes クラスターを接続する

次のコマンドを実行します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

出力:
<pre>
Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
</pre>

> [!TIP]
> location パラメーターを指定せずに上記のコマンドを実行すると、リソース グループと同じ場所に Azure Arc 対応 Kubernetes リソースが作成されます。 Azure Arc 対応 Kubernetes リソースを別の場所に作成するには、`az connectedk8s connect` コマンドの実行時に `--location <region>` または `-l <region>` のいずれかを指定します。

> [!NOTE]
> サービス プリンシパルを使用して Azure CLI にログインしている場合は、クラスターでカスタムの場所の機能を有効にするために、[追加のパラメーター](troubleshooting.md#enable-custom-locations-using-service-principal)を設定する必要があります。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

出力:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4a-connect-using-an-outbound-proxy-server"></a>4a. 送信プロキシ サーバーを使用して接続する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

クラスターが送信プロキシ サーバーの内側にある場合、Azure CLI および Azure Arc 対応 Kubernetes エージェントは、要求を送信プロキシ サーバー経由でルーティングする必要があります。

1. 送信プロキシ サーバーを使用するには、Azure CLI に必要な環境変数を設定します。

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

2. プロキシ パラメーターを指定して connect コマンドを実行します。

    ```azurecli
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

    > [!NOTE]
    > * 一部のネットワーク要求 (たとえばクラスター内のサービス間通信を含むもの) は、送信方向の通信でプロキシ サーバーを介してルーティングされるトラフィックから切り離す必要があります。 `--proxy-skip-range` パラメーターを使用して、CIDR 範囲とエンドポイントをコンマで区切って指定することで、エージェントからこれらのエンドポイントへの通信が送信プロキシ経由で行われないようにすることができます。 少なくとも、クラスター内のサービスの CIDR 範囲は、このパラメーターの値として指定する必要があります。 たとえば、`kubectl get svc -A` が返すサービスの一覧で、すべてのサービスの ClusterIP 値が `10.0.0.0/16` の範囲であるとします。 その場合、`--proxy-skip-range` に指定する値は `10.0.0.0/16,kubernetes.default.svc,.svc.cluster.local,.svc` です。
    > * `--proxy-http`、`--proxy-https`、および `--proxy-skip-range` は、ほとんどの送信プロキシ環境に必要です。 `--proxy-cert` は、プロキシが求める信頼された証明書を、エージェント ポッドの信頼された証明書ストアに挿入する必要がある場合に "*のみ*" 必要となります。
    > * 送信プロキシは、websocket 接続を許可するように構成する必要があります。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

クラスターが送信プロキシ サーバーの内側にある場合、Azure PowerShell および Azure Arc 対応 Kubernetes エージェントは、要求を送信プロキシ サーバー経由でルーティングする必要があります。

1. 送信プロキシ サーバーを使用するには、Azure PowerShell に必要な環境変数を設定します。

    ```powershell
    $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
    ```

2. 指定されたプロキシ パラメーターで connect コマンドを実行します。

    ```azurepowershell
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="5-verify-cluster-connection"></a>5. クラスターの接続を確認する

次のコマンドを実行します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s list --resource-group AzureArcTest --output table
```

Output:
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

出力:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> クラスターをオンボードした後、Azure portal の Azure Arc 対応の Kubernetes リソースの概要ページで、クラスターのメタデータ (クラスターのバージョン、エージェントのバージョン、ノードの数など) が画面に表示されるまでに約 5 ～ 10 分かかります。

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Kubernetes 用 Azure Arc エージェントを表示する

Azure Arc 対応の Kubernetes では、`azure-arc` 名前空間にいくつかのオペレーターがデプロイされます。

1. これらのデプロイとポッドは、次を使用して表示します。

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. すべてのポッドが `Running` 状態であることを確認します。

    出力:
    <pre>

    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    </pre>

## <a name="7-clean-up-resources"></a>7.リソースをクリーンアップする

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で次のコマンドを使用して、Azure Arc 対応 Kubernetes リソース、関連付けられているすべての構成リソース、"*および*" クラスターで実行されているすべてのエージェントを削除できます。

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> Azure portal を使用して Azure Arc 対応 Kubernetes リソースを削除すると、関連付けられているすべての構成リソースは削除されますが、クラスターで実行されているエージェントは削除 "*されません*"。 Azure portal の代わりに `az connectedk8s delete` を使用して Azure Arc 対応 Kubernetes リソースを削除することをお勧めします。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell で次のコマンドを使用して、Azure Arc 対応 Kubernetes リソース、関連付けられているすべての構成リソース、"*および*" クラスターで実行されているすべてのエージェントを削除できます。

```azurepowershell
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> Azure portal を使用して Azure Arc 対応 Kubernetes リソースを削除すると、関連付けられているすべての構成リソースは削除されますが、クラスターで実行されているエージェントは削除 "*されません*"。 Azure portal の代わりに `Remove-AzConnectedKubernetes` を使用して Azure Arc 対応 Kubernetes リソースを削除することをお勧めします。

---

## <a name="next-steps"></a>次のステップ

次の記事に進み、GitOps を使用して接続済みの Kubernetes クラスターに構成をデプロイする方法を確認します。
> [!div class="nextstepaction"]
> [GitOps を使用して構成をデプロイする](tutorial-use-gitops-connected-cluster.md)
