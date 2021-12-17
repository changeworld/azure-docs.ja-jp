---
title: Azure CLI で Azure Chaos Studio を使い、AKS Chaos Mesh 障害を使う実験を作成する
description: Azure CLI で AKS Chaos Mesh 障害を使う実験を作成する
author: johnkemnetz
ms.topic: how-to
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ab19c00739fea50b17ab885ffccbb397c115867e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722319"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-with-the-azure-cli"></a>Azure CLI で Chaos Mesh 障害を使うカオス実験を作成する

カオス実験を使用して、制御された環境でそれらの障害を引き起こすことで、アプリケーションに障害に対する回復性があることを確認できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、名前空間に対して Azure Kubernetes Service ポッドの定期的な障害を発生させます。 この実験を実行することは、散発的な障害が発生したときにサービスを利用できなくなるのを防ぐのに役立つ場合があります。

Azure Chaos Studio では [Chaos Mesh](https://chaos-mesh.org/) を使用します。これは、Kubernetes で AKS クラスターにフォールトを挿入するための無料のオープンソース カオス エンジニアリング プラットフォームです。 Chaos Mesh フォールトは、AKS クラスターに Chaos Mesh をインストールする必要がある[サービス直接](chaos-studio-tutorial-aks-portal.md)フォールトです。 これらの同じ手順を使用して、AKS Chaos Mesh フォールトに対する実験を設定して実行できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- AKS クラスター。 AKS クラスターがない場合は、[これらの手順に従って作成](../aks/kubernetes-walkthrough-portal.md)できます。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

> [!NOTE]
> これらの手順では、Azure Cloud Shell で Bash ターミナルを使用します。 CLI をローカルまたは PowerShell ターミナルで実行している場合、一部のコマンドは説明のように機能しない場合があります。

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>AKS クラスターで Chaos Mesh を設定する

Chaos Studio で Chaos Mesh フォールトを実行する前に、AKS クラスターに Chaos Mesh をインストールする必要があります。

1. アクティブなサブスクリプションが、AKS クラスターがデプロイされているサブスクリプションに設定されている [Azure Cloud Shell](../cloud-shell/overview.md) ウィンドウで、次のコマンドを実行します。 `$RESOURCE_GROUP` と `$CLUSTER_NAME` を、リソース グループとクラスター リソースの名前に置き換えてください。

   ```azurecli-interactive
   az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   kubectl create ns chaos-testing
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
   ```

2. 次のコマンドを実行して、Chaos Mesh ポッドがインストールされていることを確認します。

   ```azurecli-interactive
   kubectl get po -n chaos-testing
   ```

次のような出力 (chaos-controller-manager と 1 つまたは複数の chaos-daemon) が表示されるはずです。

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

また、[Chaos Mesh Web サイトのインストール手順を使用する](https://chaos-mesh.org/docs/production-installation-using-helm/)こともできます。


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>AKS クラスターで Chaos Studio を有効にする

Chaos Studio では、最初にリソースが Chaos Studio にオンボードされていない限り、リソースに対してフォールトを挿入することはできません。 リソースに[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成することによって、リソースを Chaos Studio にオンボードします。 AKS クラスターのターゲットの種類は 1 つだけ (サービス直接) ですが、その他のリソースには最大 2 つのターゲットの種類がある場合があります。つまり、サービス直接フォールト用に 1 つとエージェントベース フォールト用に 1 つです。 Chaos Mesh フォールトの種類はそれぞれ機能 (PodChaos、NetworkChaos、IOChaos など) として表されます。

1. `$RESOURCE_ID` をオンボードする AKS クラスターのリソース ID に置き換えて、ターゲットを作成します。

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. `$RESOURCE_ID` をオンボードする AKS クラスターのリソース ID に、`$CAPABILITY` を[有効にする障害機能の名前](chaos-studio-fault-library.md)に置き換えて、ターゲット上に機能を作成します。
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/$CAPABILITY?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    たとえば、PodChaos 機能を有効にする場合:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/PodChaos-2.1?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    これは、クラスター上で有効にする各機能に対して行う必要があります。

これで、AKS クラスターが Chaos Studio に正常にオンボードされました。

## <a name="create-an-experiment"></a>実験の作成
これで AKS クラスターがオンボードされたので、実験を作成できます。 カオス実験では、ターゲット リソースに対して実行するアクションを定義します。これらは、順次実行されるステップと、並列で実行される分岐に分けられます。

1. Chaos Mesh の jsonSpec を作成します。
    1. フォールトの種類 (たとえば、[PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files) という種類) については、Chaos Mesh のドキュメントを参照してください。
    2. Chaos Mesh のドキュメントを使用して、そのフォールトの種類の YAML 構成を作成します。

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: all
          duration: '600s'
          selector:
            namespaces:
              - default
        ```
    3. (spec プロパティ名を含む) `spec` の外側の YAML を削除し、spec の詳細のインデントを解除します。

        ```yaml
        action: pod-failure
        mode: all
        duration: '600s'
        selector:
          namespaces:
            - default
        ```
    4. [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して、Chaos Mesh YAML を JSON に変換し、それを最小化します。

        ```json
        {"action":"pod-failure","mode":"all","duration":"600s","selector":{"namespaces":["default"]}}
        ```
    5. [このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使い、JSON 仕様をエスケープします。
    
        ```json
        {\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}
        ```

2. 以下の JSON サンプルから、実験用の JSON を作成します。 [実験の作成 API](/rest/api/chaosstudio/experiments/create-or-update)、[障害ライブラリ](chaos-studio-fault-library.md)、前の手順で作成した jsonSpec を使って、実行する実験に対応するように JSON を変更します。

    ```json
    {
      "location": "centralus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "AKS pod kill",
            "branches": [
              {
                "name": "AKS pod kill",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                          "key": "jsonSpec",
                          "value": "{\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}"
                      }
                    ],
                    "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換えて、Azure CLI を使って実験を作成します。 実験用の JSON を保存してアップロードし、`experiment.json` を JSON ファイル名で更新したことを確認します。

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    実験ごとに、対応するシステム割り当てマネージド ID が作成されます。 次のステップの応答でこれが識別できるように、`principalId` をメモします。

## <a name="give-experiment-permission-to-your-aks-cluster"></a>AKS クラスターに実験のアクセス許可を付与する
カオス実験を作成すると、Chaos Studio によって、ターゲット リソースに対してフォールトを実行するシステム割り当てマネージド ID が作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。

`$EXPERIMENT_PRINCIPAL_ID` を前の手順の principalId に置き換え、`$RESOURCE_ID` をターゲット リソースのリソース ID (この場合は AKS クラスターのリソース ID) に置き換え、次のコマンドを使い、実験にリソースへのアクセスを許可します。 実験の対象となるリソースごとに、このコマンドを実行します。 

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Cluster User Role" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するには、AKS クラスターの概要を開き、別のブラウザー タブで **[分析情報]** に移動することをお勧めします。 **[アクティブなポッド数]** のライブ データには、実験を実行した場合の影響が表示されます。

1. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換え、Azure CLI を使って実験を開始します。

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 応答には、実験の実行時に実験の状態を照会するために使用できる状態 URL が含まれています。

## <a name="next-steps"></a>次のステップ
これで AKS Chaos Mesh サービス直接実験を実行したので、次のことを行う準備ができました。
- [エージェントベース フォールトを使用する実験を作成する](chaos-studio-tutorial-agent-based-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)
