---
title: Kubernetes インスタンスの種類を作成して選択する方法 (プレビュー)
description: Azure Machine Learning で、トレーニング ワークロードと推論ワークロード用の Azure Arc 対応 Kubernetes クラスター インスタンスの種類を作成して選択します。
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46ae66658d5f07e1a44322bf64997a80d66db1c0
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485169"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Kubernetes インスタンスの種類を作成して選択する (プレビュー)

Azure Arc 対応 Kubernetes クラスターで Azure Machine Learning のトレーニング ワークロードと推論ワークロード用の Kubernetes インスタンスを作成して選択する方法について説明します。

## <a name="what-are-instance-types"></a>インスタンスの種類とは

インスタンスの種類とは、トレーニング ワークロードと推論ワークロード用に特定の種類の計算ノードをターゲットにできる、Azure Machine Learning の概念です。  Azure VM の場合、インスタンスの種類の例としては `STANDARD_D2_V3` などがあります。

Kubernetes クラスターでは、インスタンスの種類は次の 2 つの要素によって定義されます。

* [nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) - `nodeSelector` を使用すると、ポッドを実行するノードを指定できます。  ノードには、対応するラベルが必要です。
* [resources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) - `resources` セクションでは、ポッドのコンピューティング リソース (CPU、メモリ、Nvidia GPU) を定義します。

## <a name="create-instance-types"></a>インスタンスの種類を作成する

インスタンスの種類は、Azure Machine Learning の拡張機能と共にインストールされるカスタム リソース定義 (CRD) で表されます。  

### <a name="create-a-single-instance-type"></a>1 つのインスタンスの種類を作成する

新しいインスタンスの種類を作成するには、CRD というインスタンスの種類の新しいカスタム リソースを作成します。  

たとえば、CRD `my_instance_type.yaml` があるとします。

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceType
metadata:
  name: myinstancetypename
spec:
  nodeSelector:
    mylabel: mylabelvalue
  resources:
    limits:
      cpu: "1"
      nvidia.com/gpu: 1
      memory: "2Gi"
    requests:
      cpu: "700m"
      memory: "1500Mi"
```

`kubectl apply` コマンドを使って、新しいインスタンスの種類を作成します。

```bash
kubectl apply -f my_instance_type.yaml
```

この操作では、次のプロパティを持つインスタンスの種類を作成します。

- ポッドは、ラベル `mylabel: mylabelvalue` が付いたノードでのみスケジュールされます。
- ポッドには、`700m` CPU と `1500Mi` メモリのリソース要求が割り当てられます。 
- ポッドには、`1` CPU、`2Gi` メモリ、`1` Nvidia GPU のリソース制限が割り当てられます。

> [!NOTE]
> CRD を指定するときは、次の規則に注意してください。
> - Nvidia GPU リソースは、`limits` セクションでのみ指定されます。  詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins)を参照してください。
> - CPU とメモリのリソースは文字列値です。
> - CPU は、ミリコア単位 (`100m`) または完全な数字 (`"1"` (`1000m` と等価)) で指定できます。
> - メモリは、完全な数字 + サフィックスとして指定できます (`1024Mi` は `1024 MiB` を表します)。

### <a name="create-multiple-instance-types"></a>複数のインスタンスの種類を作成する

複数の CRD を使って、複数のインスタンスの種類を一度に作成することもできます。

たとえば、CRD `my_instance_type.yaml` があるとします。

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceTypeList
items:
  - metadata:
      name: cpusmall
    spec:
      resources:
        limits:
          cpu: "100m"
          nvidia.com/gpu: 0
          memory: "10Mi"
        requests:
          cpu: "100m"
          memory: "1Gi"

  - metadata:
      name: defaultinstancetype
    spec:
      resources:
        limits:
          cpu: "1"
          nvidia.com/gpu: 0
          memory: "1Gi"
        requests:
          cpu: "1"
          memory: "1Gi" 
```

`kubectl apply` コマンドを使って、複数のインスタンスの種類を作成します。

```bash
kubectl apply -f my_instance_type.yaml
``` 

この操作では、2 つのインスタンスの種類を作成します。1 つは `defaultinstancetype`、もう 1 つは `cpusmall` で、リソース仕様が異なります。 既定のインスタンスの種類について詳しくは、このドキュメントの「[既定のインスタンスの種類](#default-instance-types)」セクションを参照してください。

## <a name="default-instance-types"></a>既定のインスタンスの種類

インスタンスの種類を指定せずにトレーニング ワークロードまたは推論ワークロードを送信すると、既定のインスタンスの種類が使用されます。  

Kubernetes クラスターの既定のインスタンスの種類を指定するには、`defaultinstancetype` という名前のインスタンスの種類を作成し、他のインスタンスの種類と同様に `nodeSelector` プロパティと `resources` プロパティをそれぞれ定義します。  そのインスタンスの種類は自動的に既定値として認識されます。

既定のインスタンスの種類が定義されていない場合は、次の既定の動作が適用されます。

* nodeSelector は適用されません。つまり、ポッドは任意のノードでスケジュールできます。
* ワークロードのポッドには、既定のリソースが割り当てられます。

    ```yaml
    resources:
        limits:
        cpu: "0.6"
        memory: "1536Mi"
        requests:
        cpu: "0.6"
        memory: "1536Mi"
    ```

> [!IMPORTANT]
> 既定のインスタンスの種類はクラスターで InstanceType カスタム リソースとして表示されませんが、すべてのクライアント (Azure Machine Learning スタジオ、Azure CLI、Python SDK) で表示されます。

## <a name="select-an-instance-type-for-training-workloads"></a>トレーニング ワークロード用のインスタンスの種類を選ぶ

Azure Machine Learning 2.0 CLI を使ってトレーニング ジョブのインスタンスの種類を選ぶには、YAML 仕様ファイルの `compute` セクションの一部としてその名前を指定します。  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

上記の例では、`<compute_target_name>` を Kubernetes コンピューティング先の名前に、`<instance_type_name>` を選択したいインスタンスの種類の名前に置き換えます。

> [!TIP]
> 既定のインスタンスの種類では、意図的にリソースがほとんど使用されません。 すべての機械学習ワークロードが適切なリソースで正常に実行されるようにするには、カスタムのインスタンスの種類を作成することを強くお勧めします。

## <a name="select-an-instance-type-for-inferencing-workloads"></a>推論ワークロード用のインスタンスの種類を選ぶ

Azure Machine Learning 2.0 CLI を使って推論ワークロード用のインスタンスの種類を選ぶには、`deployments` セクションの一部としてその名前を指定します。  たとえば、次のように入力します。

```yaml
type: online
auth_mode: key
target: azureml:<your compute target name>
traffic:
  blue: 100

deployments:
  - name: blue
    app_insights_enabled: true
    model: 
      name: sklearn_mnist_model
      version: 1
      local_path: ./model/sklearn_mnist_model.pkl
    code_configuration:
      code: 
        local_path: ./script/
      scoring_script: score.py
    instance_type: <instance_type_name>
    environment: 
      name: sklearn-mnist-env
      version: 1
      path: .
      conda_file: file:./model/conda.yml
      docker:
        image: mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1
```

上の例では、`<instance_type_name>` を選択したいインスタンスの種類の名前に置き換えます。

## <a name="next-steps"></a>次のステップ

- [Azure Arc 対応機械学習を構成する (プレビュー)](how-to-attach-arc-kubernetes.md)
- [CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する)](how-to-train-cli.md)
- [オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
