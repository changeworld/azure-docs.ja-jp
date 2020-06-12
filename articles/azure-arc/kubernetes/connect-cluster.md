---
title: Azure Arc 対応の Kubernetes クラスターを接続する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応の Kubernetes クラスターを Azure Arc と接続する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.custom: references_regions
ms.openlocfilehash: 868964361e6089eb3417b0f2e2681d82d4aa0b75
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299645"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure Arc 対応の Kubernetes クラスターを接続する (プレビュー)

Kubernetes クラスターを Azure Arc に接続します。

## <a name="before-you-begin"></a>開始する前に

次の要件の準備ができていることを確認します。

* 稼働している Kubernetes クラスター
* kubeconfig のアクセス権と、クラスター管理者のアクセス権が必要です。
* `az login` および `az connectedk8s connect` コマンドで使用されるユーザーまたはサービス プリンシパルには、"Microsoft.Kubernetes/connectedclusters" リソースの種類に対する "読み取り" と "書き込み" のアクセス許可が必要です。 オンボード用に Azure CLI で使用されるユーザーまたはサービス プリンシパルに対するロールの割り当てには、これらのアクセス許可を持つ "Azure Arc for Kubernetes のオンボード" ロールを使用できます。
* *connectedk8s* および *k8sconfiguration* 拡張機能の最新バージョン

## <a name="supported-regions"></a>サポートされているリージョン

* 米国東部
* 西ヨーロッパ

## <a name="network-requirements"></a>ネットワークの要件

Azure Arc エージェントが機能するには、次のプロトコル、ポート、送信 URL が必要です。

* ポート 443 での TCP --> `https://:443`
* ポート 9418 での TCP --> `git://:9418`

| エンドポイント (DNS)                                                                                               | 説明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | エージェントが Azure に接続してクラスターを登録するために必要です                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | エージェントが状態をプッシュして構成情報をフェッチするためのデータ プレーン エンドポイント                                      |
| `https://docker.io`                                                                                            | コンテナー イメージをプルするために必要です                                                                                         |
| `https://github.com`、git://github.com                                                                         | GitOps リポジトリの例は、GitHub でホストされています。 構成エージェントには、指定する git エンドポイントへの接続が必要です。 |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager トークンをフェッチして更新するために必要です                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Azure Arc エージェント用のコンテナー イメージをプルするために必要です                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用の 2 つのプロバイダーを登録する:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

登録は非同期プロセスです。 登録には、10 分ほどかかる場合があります。 次のコマンドを使用して、登録プロセスを監視できます。

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-and-arc-enabled-kubernetes-extensions"></a>Azure CLI および Arc 対応 Kubernetes 拡張機能をインストールする
Azure Arc 対応 Kubernetes CLI 拡張機能をインストールするには、Azure CLI バージョン2.3 以降が必要です。 [Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)するか、最新バージョンに更新して、Azure CLI バージョン 2.3 以降があるようにしてください。

Kubernetes クラスターを Azure に接続するために必要な `connectedk8s` 拡張機能をインストールします。

```console
az extension add --name connectedk8s
```

`k8sconfiguration` 拡張機能をインストールします。

```console
az extension add --name k8sconfiguration
```

次のコマンドを実行して、拡張機能を最新バージョンに更新します。

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="install-helm"></a>Helm のインストール
connectedk8s 拡張機能を使用してクラスターをオンボードするには、Helm 3 が必要です。 この要件を満たすには、[最新リリースの Helm 3 をインストール](https://helm.sh/docs/intro/install)してください。

## <a name="create-a-resource-group"></a>リソース グループを作成します

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

次に、Kubernetes クラスターを Azure に接続します。 `az connectedk8s connect` のワークフローは次のとおりです。

1. `KUBECONFIG`、`~/.kube/config`、または `--kube-config` で、Kubernetes クラスターへの接続を確認します
1. Helm 3 を使用して、Kubernetes 用の Azure Arc エージェントを `azure-arc` 名前空間にデプロイします

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
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>接続されたクラスターを検証する

接続されているクラスターの一覧を表示します。

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

このリソースは、[Azure プレビュー ポータル](https://preview.portal.azure.com/)で表示することもできます。 ブラウザーでポータルを開いたら、先ほど `az connectedk8s connect` コマンドで使用したリソース名とリソース グループ名の入力に基づいて、リソース グループと Azure Arc 対応の Kubernetes リソースに移動します

Azure Arc 対応の Kubernetes では、`azure-arc` 名前空間にいくつかのオペレーターがデプロイされます。 これらのデプロイとポッドを次のようにして表示できます。

```console
kubectl -n azure-arc get deployments,pods
```

**出力:**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Kubernetes 用 Azure Arc エージェント

Azure Arc 対応の Kubernetes は、`azure-arc` 名前空間にデプロイされたクラスター内で実行されるいくつかのエージェント (オペレーター) で構成されています。

* `deployment.apps/config-agent`: クラスターに適用されるソース管理構成リソースの接続されたクラスターを監視し、コンプライアンス状態を更新します
* `deployment.apps/controller-manager`: オペレーターのオペレーターであり、Azure Arc コンポーネント間の相互作用を統制します
* `deployment.apps/metrics-agent`: 他の Arc エージェントのメトリックを収集し、これらのエージェントのパフォーマンスが最適であることを確認します
* `deployment.apps/cluster-metadata-operator`: クラスターのメタデータを収集します (クラスターのバージョン、ノード数、Arc エージェントのバージョン)
* `deployment.apps/resource-sync-agent`: 前述のクラスター メタデータを Azure に同期します
* `deployment.apps/clusteridentityoperator`: 他のエージェントが Azure との通信に使用する管理サービス ID (MSI) 証明書を保持します
* `deployment.apps/flux-logs-agent`: ソース管理構成の一部としてデプロイされる Flux オペレーターからログを収集します

## <a name="delete-a-connected-cluster"></a>接続されているクラスターを削除する

Azure CLI または Azure portal を使用して `Microsoft.Kubernetes/connectedcluster` リソースを削除できます。


* **Azure CLI を使用した削除**:次の Azure CLI コマンドを使用して、Azure Arc 対応の Kubernetes リソースの削除を開始できます。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  これにより、Azure 内の `Microsoft.Kubernetes/connectedCluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されます。 Azure CLI では、Helm のアンインストールを使用して、クラスターで実行されているエージェントが削除されます。

* **Azure portal での削除**:Azure portal で Azure Arc 対応の Kubernetes リソースを削除すると、Azure 内の `Microsoft.Kubernetes/connectedcluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されますが、クラスターで実行されているエージェントは削除されません。 クラスターで実行されているエージェントを削除するには、次のコマンドを実行します。

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>次のステップ

* [接続されたクラスターで GitOps を使用する](./use-gitops-connected-cluster.md)
* [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)