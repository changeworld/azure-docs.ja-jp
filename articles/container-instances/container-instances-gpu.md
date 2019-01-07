---
title: GPU 対応 Azure コンテナー インスタンスをデプロイする
description: GPU リソースで実行する Azure コンテナー インスタンスをデプロイする方法について説明します。
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: d02ae48bab6a17cbf5568996b30ccb39ccb81c59
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994012"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU リソースを使用するコンテナー インスタンスをデプロイする

特定のコンピューティング集中型ワークロードを Azure Container Instances で実行するには、*GPU リソース*を持つコンテナー グループをデプロイします。 コンテナー インスタンスは、CUDA やディープ ラーニング アプリケーションなどのコンテナー ワークロードを実行しながら、1 つまたは複数の NVIDIA Tesla GPU にアクセスできます。

この記事に示すように、[YAML ファイル](container-instances-multi-container-yaml.md)または [Resource Manager テンプレート](container-instances-multi-container-group.md)を使用して、コンテナー グループをデプロイするときに GPU リソースを追加することができます。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="preview-limitations"></a>プレビューの制限事項

プレビューでは、コンテナー グループで GPU リソースを使用する場合に、次の制限が適用されます。 

**サポートされているリージョン**:

* 米国東部 (eastus)
* 米国西部 2 (westus2)
* 米国中南部 (southcentralus)
* 西ヨーロッパ (westeurope)
* 北ヨーロッパ (northeurope)
* 東アジア (eastasia)
* インド中部 (centralindia)

サポート対象リージョンは今後追加される予定です。

**サポートされている OS の種類**:Linux のみ

**追加の制限事項**:GPU リソースは、コンテナー グループを[仮想ネットワーク](container-instances-vnet.md)にデプロイするときには使用できません。

## <a name="about-gpu-resources"></a>GPU リソースについて

### <a name="count-and-sku"></a>カウントと SKU

コンテナー インスタンスで GPU を使用するには、次の情報を使って *GPU リソース*を指定します。

* **カウント** - GPU の数:**1**、**2**、または **4**。
* **SKU** - GPU SKU:**K80**、**P100**、または **V100**。 各 SKU は、次のいずれかの Azure GPU 対応 VM ファミリの NVIDIA Tesla GPU にマップされます。

  | SKU | VM ファミリ |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

### <a name="cpu-and-memory"></a>CPU とメモリ

GPU リソースをデプロイするときに、ワークロードに適した CPU とメモリ リソースを設定します。最大値を次の表に示します。 これらの値は、現在、GPU リソースのないコンテナー インスタンスの CPU とメモリの制限よりも大きくなっています。  

| GPU SKU | GPU 数 | CPU |  メモリ (GB) |
| --- | --- | --- | --- |
| K80 | 1 | 6 | 56 |
| K80 | 2 | 12 | 112 |
| K80 | 4 | 24 | 224 |
| P100 | 1 | 6 | 112 |
| P100 | 2 | 12 | 224 |
| P100 | 4 | 24 | 448 |
| V100 | 1 | 6 | 112 |
| V100 | 2 | 12 | 224 |
| V100 | 4 | 24 | 448 |

### <a name="things-to-know"></a>注意事項

* **デプロイ時**- GPU リソースを含むコンテナー グループの作成には、最大で **8 - 10 分**かかります。 これは、Azure で GPU VM をプロビジョニングして構成するための追加時間によるものです。 

* **価格**- GPU リソースのないコンテナー グループと同様に、Azure では GPU リソースがあるコンテナー グループの*期間*にわたって使用されたリソースに対して請求されます。 期間は、最初のコンテナー イメージのプルが開始された時点から、コンテナー グループが終了する時点までが計算されます。 コンテナー グループをデプロイする時間は含まれません。

  価格は、GPU リソースがないコンテナー グループよりも GPU リソースがあるコンテナー グループの方が高くなります。 [価格の詳細](https://azure.microsoft.com/pricing/details/container-instances/)を参照してください。

* **CUDA ドライバー** - GPU リソースがあるコンテナー インスタンスは、NVIDIA CUDA ドライバーとコンテナーのランタイムを使用して事前にプロビジョニングされているため、CUDA ワークロード用に開発されたコンテナー イメージを使用できます。

## <a name="yaml-example"></a>YAML の例

次の YAML を *gpu-deploy-aci.yaml* という名前の新しいファイルにコピーしてから、ファイルを保存します。 この YAML により、K80 GPU を持つコンテナー インスタンスを指定する *gpucontainergroup* という名前のコンテナー グループが作成されます。 このインスタンスでは、CUDA ベクトル加法アプリケーションのサンプルが実行されます。 ワークロードを実行するには、リソース要求だけで十分です。

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

[az container create][az-container-create] コマンドを使って、`--file` パラメーターに YAML ファイル名を指定して、コンテナー グループをデプロイします。 リソース グループの名前と、GPU リソースをサポートするコンテナー グループの場所 (*eastus* など) を指定する必要があります。  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

デプロイが完了するまで、数分間かかります。 その後、コンテナーが起動して CUDA ベクトル加法演算が実行されます。 [az container logs][az-container-logs] コマンドを実行して、ログの出力を表示します。

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

出力:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager テンプレートの例

まず、`gpudeploy.json` という名前のファイルを作成し、次の JSON をそのファイルにコピーします。 この例では、[MNIST データセット](http://yann.lecun.com/exdb/mnist/)に対して [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) トレーニング ジョブを実行する V100 GPU を搭載したコンテナー インスタンスをデプロイします。 ワークロードを実行するには、リソース要求だけで十分です。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

[az group deployment create][az-group-deployment-create] コマンドで、テンプレートをデプロイします。 GPU リソースをサポートしているリージョン (*eastus* など) で作成されたリソース グループの名前を指定する必要があります。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

デプロイが完了するまで、数分間かかります。 その後、コンテナーが起動し、TensorFlow ジョブが実行されます。 [az container logs][az-container-logs] コマンドを実行して、ログの出力を表示します。

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

出力:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

GPU のリソースの使用は高価になる可能性があるため、コンテナーが長期間にわたり予期せず実行されていることがないようにします。 Azure portal でコンテナーを監視するか、[az container show][az-container-show] コマンドを使用して、コンテナー グループの状態を確認します。 例: 

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

作成したコンテナー インスタンスの操作が完了したら、次のコマンドを使ってそれらを削除します。

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>次の手順

* [YAML ファイル](container-instances-multi-container-yaml.md)または [Resource Manager テンプレート](container-instances-multi-container-group.md)を使用したコンテナー グループのデプロイについて学習します。
* Azure での [GPU 最適化済み VM サイズ](../virtual-machines/linux/sizes-gpu.md)について学習します。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
