---
title: 'クイックスタート: 既存の Kubernetes クラスターを Azure Arc に接続する'
description: このクイックスタートでは、Azure Arc 対応 Kubernetes クラスターを接続する方法について説明します。
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, クラスター
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481244"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>クイックスタート: 既存の Kubernetes クラスターを Azure Arc に接続する 

このクイックスタートでは、Azure Arc 対応 Kubernetes のメリットを活用し、既存の Kubernetes クラスターを Azure Arc に接続します。Azure Arc にクラスターを接続するという概念については、[Azure Arc 対応 Kubernetes エージェントのアーキテクチャに関する記事](./conceptual-agent-architecture.md)をご覧ください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* 稼働している Kubernetes クラスター。 お持ちでない場合は、次のいずれかのオプションを使用してクラスターを作成できます。
    * [Docker 内の Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する
    * [クラスター API](https://cluster-api.sigs.k8s.io/user/quick-start.html) を使用したセルフマネージド Kubernetes クラスター

    >[!NOTE]
    > クラスターには、オペレーティング システムとアーキテクチャの種類が `linux/amd64` であるノードが少なくとも 1 つ含まれている必要があります。 `linux/arm64` ノードのみが含まれるクラスターはまだサポートされていません。
    
* `kubeconfig` ファイルと、クラスターを指すコンテキスト。
* Azure Arc 対応 Kubernetes リソースの種類 (`Microsoft.Kubernetes/connectedClusters`) に対する "読み取り" および "書き込み" アクセス許可。

* [Helm 3 の最新リリース](https://helm.sh/docs/intro/install)をインストールします。

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](https://docs.microsoft.com/cli/azure/install-azure-cli)
* `connectedk8s` Azure CLI 拡張機能バージョン 1.0.0 以降をインストールします。
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> `connectedk8s` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新してください - `az extension update --name connectedk8s`


>[!NOTE]
>Azure Arc 対応 Kubernetes でサポートされているリージョンの一覧については、[こちら](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)を参照してください。

>[!NOTE]
> クラスターでカスタムの場所を使用する場合、カスタムの場所は現時点では米国東部または西ヨーロッパ リージョンでのみ利用可能であるため、クラスターの接続にはこれらのリージョンを使用してください。 その他すべての Azure Arc 対応 Kubernetes 機能は、上記のすべてのリージョンでご利用いただけます。

## <a name="meet-network-requirements"></a>ネットワーク要件を満たす

>[!IMPORTANT]
>Azure Arc エージェントが機能するには、次のプロトコル、ポート、送信 URL が必要です。
>* ポート 443 での TCP: `https://:443`
>* ポート 9418 での TCP: `git://:9418`
  
| エンドポイント (DNS) | 説明 |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | エージェントが Azure に接続し、クラスターを登録するために必要です。                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | エージェントが状態をプッシュして構成情報をフェッチするためのデータ プレーン エンドポイント。                                      |  
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager トークンをフェッチし、更新するために必要です。                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Azure Arc エージェント用のコンテナー イメージをプルするために必要です。                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  システムによって割り当てられたマネージド サービス ID (MSI) 証明書をプルするために必要です。                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用の 2 つのプロバイダーを登録する

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

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成します。  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>既存の Kubernetes クラスターを接続する

1. 次のコマンドを使用して、Kubernetes クラスターを Azure Arc に接続します。
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
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
    ```

> [!TIP]
> location パラメーターを指定せずに上記のコマンドを実行すると、リソース グループと同じ場所に Azure Arc 対応 Kubernetes リソースが作成されます。 Azure Arc 対応 Kubernetes リソースを別の場所に作成するには、`az connectedk8s connect` コマンドの実行時に `--location <region>` または `-l <region>` のいずれかを指定します。

## <a name="verify-cluster-connection"></a>クラスターの接続を確認する

次のコマンドを使用して、接続済みのクラスターの一覧を表示します。  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> クラスターをオンボードした後、Azure portal の Azure Arc 対応 Kubernetes リソースの概要ページで、クラスターのメタデータ (クラスターのバージョン、エージェントのバージョン、ノードの数など) が画面に表示されるまでに約 5 ～ 10 分かかります。

## <a name="connect-using-an-outbound-proxy-server"></a>送信プロキシ サーバーを使用して接続する

クラスターが送信プロキシ サーバーの内側にある場合、Azure CLI および Azure Arc 対応 Kubernetes エージェントは、要求を送信プロキシ サーバー経由でルーティングする必要があります。 


1. 送信プロキシ サーバーを使用するには、Azure CLI に必要な環境変数を設定します。

    * bash を使用している場合、適切な値で次のコマンドを実行します。

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * PowerShell を使用している場合、適切な値で次のコマンドを実行します。

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. プロキシ パラメーターを指定して connect コマンドを実行します。

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * `--proxy-skip-range` で `excludedCIDR` を指定して、エージェントのクラスター内通信が切断されないようにします。
> * `--proxy-http`、`--proxy-https`、および `--proxy-skip-range` は、ほとんどの送信プロキシ環境に必要です。 `--proxy-cert` は、プロキシが求める信頼された証明書を、エージェント ポッドの信頼された証明書ストアに挿入する必要がある場合に "*のみ*" 必要となります。

## <a name="view-azure-arc-agents-for-kubernetes"></a>Kubernetes 用 Azure Arc エージェントを表示する

Azure Arc 対応の Kubernetes では、`azure-arc` 名前空間にいくつかのオペレーターがデプロイされます。 

1. これらのデプロイとポッドは、次を使用して表示します。

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. すべてのポッドが `Running` 状態であることを確認します。

    ```output
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
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure CLI で次のコマンドを使用して、Azure Arc 対応 Kubernetes リソース、関連付けられているすべての構成リソース、"*および*" クラスターで実行されているすべてのエージェントを削除できます。

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Azure portal を使用して Azure Arc 対応 Kubernetes リソースを削除すると、関連付けられているすべての構成リソースは削除されますが、クラスターで実行されているエージェントは削除 "*されません*"。 Azure portal の代わりに `az connectedk8s delete` を使用して Azure Arc 対応 Kubernetes リソースを削除することをお勧めします。

## <a name="next-steps"></a>次のステップ

次の記事に進み、GitOps を使用して接続済みの Kubernetes クラスターに構成をデプロイする方法を確認します。
> [!div class="nextstepaction"]
> [GitOps を使用して構成をデプロイする](tutorial-use-gitops-connected-cluster.md)
