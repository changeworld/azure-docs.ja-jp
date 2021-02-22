---
title: Azure Arc 対応 Kubernetes クラスターを接続する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応 Kubernetes クラスターを Azure Arc と接続する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5e2058c5128075de4c37eb9768b204532cd09ffa
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558548"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure Arc 対応 Kubernetes クラスターを接続する (プレビュー)

この記事では、既存の Kubernetes クラスターを Azure Arc に接続する手順について説明します。概念の概要については、[こちら](./conceptual-agent-architecture.md)を参照してください。

## <a name="before-you-begin"></a>開始する前に

次の前提条件を満たしていることを確認します。

* 稼働している Kubernetes クラスター。 既存の Kubernetes クラスターがない場合は、次のいずれかのガイドを使用してテスト クラスターを作成できます。
  * [Docker 内の Kubernetes (kind)](https://kind.sigs.k8s.io/) を使用して Kubernetes クラスターを作成する。
  * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する。
* Arc 対応 Kubernetes エージェントのデプロイ用の、クラスターとクラスター上のクラスター管理者ロールにアクセスするための kubeconfig ファイル。
* `az login` および `az connectedk8s connect` コマンドで使用されるユーザーまたはサービス プリンシパルには、"Microsoft.Kubernetes/connectedclusters" リソースの種類に対する "読み取り" と "書き込み" のアクセス許可が必要です。 ユーザーまたはサービス プリンシパルに対するロールの割り当てには、これらのアクセス許可を持つ "Kubernetes クラスター - Azure Arc のオンボード" ロールを使用できます。
* `connectedk8s` 拡張機能を使用してクラスターをオンボードするための Helm 3。 この要件を満たすには、[最新リリースの Helm 3 をインストール](https://helm.sh/docs/intro/install)してください。
* Azure Arc 対応 Kubernetes CLI 拡張機能をインストールするための Azure CLI バージョン 2.15 以降。 [Azure CLI をインストールする](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)か、最新バージョンに更新します。
* Azure Arc 対応 Kubernetes CLI 拡張機能をインストールします。
  
  * Kubernetes クラスターを Azure に接続するために必要な `connectedk8s` 拡張機能をインストールします。
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * `k8sconfiguration` 拡張機能をインストールします。
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * これらの拡張機能を後で更新する場合は、次のコマンドを実行します。
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>サポートされているリージョン

* 米国東部
* 西ヨーロッパ

## <a name="network-requirements"></a>ネットワークの要件

Azure Arc エージェントが機能するには、次のプロトコル、ポート、送信 URL が必要です。

* ポート 443 での TCP: `https://:443`
* ポート 9418 での TCP: `git://:9418`

| エンドポイント (DNS)                                                                                               | 説明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | エージェントが Azure に接続し、クラスターを登録するために必要です。                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | エージェントが状態をプッシュして構成情報をフェッチするためのデータ プレーン エンドポイント。                                      |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager トークンをフェッチし、更新するために必要です。                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Azure Arc エージェント用のコンテナー イメージをプルするために必要です。                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  システムによって割り当てられたマネージド ID 証明書をプルするために必須。                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用の 2 つのプロバイダーを登録する

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

登録は非同期プロセスであり、約 10 分かかる場合があります。 次のコマンドを使用して、登録プロセスを監視できます。

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

クラスターのメタデータを格納するには、リソース グループを使用します。

最初に、接続されたクラスター リソースを保持するためのリソース グループを作成します。

```console
az group create --name AzureArcTest -l EastUS -o table
```

**出力:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>クラスターを接続する

次に、`az connectedk8s connect` を使用して Kubernetes クラスターを Azure に接続します。

1. 次のいずれかを使用して、Kubernetes クラスターへの接続を確認します。
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Helm 3 を使用して、Kubernetes 用の Azure Arc エージェントを `azure-arc` 名前空間にデプロイします。

    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

**出力:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
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

## <a name="verify-connected-cluster"></a>接続されたクラスターを検証する

接続されているクラスターの一覧を表示するには、次のコマンドを使用します。

```console
az connectedk8s list -g AzureArcTest -o table
```

**出力:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

このリソースは、[Azure portal](https://portal.azure.com/) で表示することもできます。 ブラウザーでポータルを開き、先ほど `az connectedk8s connect` コマンドで使用したリソース名とリソース グループ名の入力に基づいて、リソース グループと Azure Arc 対応 Kubernetes リソースに移動します。  
> [!NOTE]
> クラスターをオンボードした後、Azure portal の Azure Arc 対応 Kubernetes リソースの概要ページで、クラスターのメタデータ (クラスターのバージョン、エージェントのバージョン、ノードの数など) が画面に表示されるまでに約 5 ～ 10 分かかります。

## <a name="connect-using-an-outbound-proxy-server"></a>送信プロキシ サーバーを使用して接続する

クラスターが送信プロキシ サーバーの内側にある場合、Azure CLI および Azure Arc 対応 Kubernetes エージェントは、要求を送信プロキシ サーバー経由でルーティングする必要があります。

1. お使いのコンピューターにインストールされている `connectedk8s` 拡張機能のバージョンを確認します。

    ```console
    az -v
    ```

    送信プロキシを使用してエージェントを設定するには、`connectedk8s` 拡張機能のバージョン 0.2.5 以降が必要です。 お使いのコンピューターに 0.2.3 以前のバージョンがある場合は、[更新手順](#before-you-begin)に従って、そのコンピューター上に最新バージョンの拡張機能を取得してください。

2. 送信プロキシ サーバーを使用するには、Azure CLI に必要な環境変数を設定します。

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

3. プロキシ パラメーターを指定して connect コマンドを実行します。

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * `--proxy-skip-range` で `excludedCIDR` を指定することは、エージェントのクラスター内通信が切断されないようにするために重要です。
> * ほとんどの送信プロキシ環境では `--proxy-http`、`--proxy-https`、`--proxy-skip-range` が求められますが、プロキシからの信頼されている証明書を、エージェント ポッドの信頼されている証明書ストアに挿入する必要がある場合は、`--proxy-cert` のみが必要となります。
> * 上記のプロキシ仕様は現在、sourceControlConfiguration で使用される Flux ポッドではなく、Arc エージェントに対してのみ適用されます。 Azure Arc 対応 Kubernetes チームはこの機能に積極的に取り組んでいるため、間もなく利用できるようになります。

## <a name="azure-arc-agents-for-kubernetes"></a>Kubernetes 用 Azure Arc エージェント

Azure Arc 対応の Kubernetes では、`azure-arc` 名前空間にいくつかのオペレーターがデプロイされます。 これらのデプロイとポッドを次のようにして表示できます。

```console
kubectl -n azure-arc get deployments,pods
```

**出力:**

```console
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

すべてのポッドが `Running` 状態であることを確認します。

## <a name="delete-a-connected-cluster"></a>接続されているクラスターを削除する

Azure CLI または Azure portal を使用して `Microsoft.Kubernetes/connectedcluster` リソースを削除できます。


* **Azure CLI を使用した削除**:次の Azure CLI コマンドを使用して、Azure Arc 対応 Kubernetes リソースの削除を開始します。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  このコマンドにより、Azure 内の `Microsoft.Kubernetes/connectedCluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されます。 Azure CLI では、`helm uninstall` を使用して、クラスターで実行されているエージェントが削除されます。

* **Azure portal での削除**:Azure portal で Azure Arc 対応 Kubernetes リソースを削除すると、Azure 内の `Microsoft.Kubernetes/connectedcluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されますが、クラスターで実行されているエージェントは削除 "*されません*"。 

  クラスターで実行されているエージェントを削除するには、次のコマンドを実行します。

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>次のステップ

* [接続されたクラスターで GitOps を使用する](./use-gitops-connected-cluster.md)
* [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
